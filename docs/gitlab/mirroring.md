# GitLab – Mirroring

#### Auto Sync via GitLab UI
Open your GitLab repo and then go to **Settings > Repository > Mirroring repositories** (https://gitlab.com/USERNAME/PROJECT-NAME/-/settings/repository)

Wichtig: Mirroring privater Repos ist kostenpflichtig (GitLab Premium).

#### Mirror Github → GitLab (Script)


# Mirror GitHub to GitLab


Then run the script:
```shell
# Because gitlab is locally running with a self-signed certificate
export NODE_TLS_REJECT_UNAUTHORIZED=0

# https://github.com/settings/tokens
export GITHUB_TOKEN='ghp_xxx'
# https://gitlab.local.com/-/user_settings/personal_access_tokens?page=1&state=active&sort=expires_asc
export GITLAB_TOKEN='glpat_xxx'
# https://gitlab.local.com
export GITLAB_BASE_URL='http://<gitlab-host-oder-ingress>'

export GITLAB_NAMESPACE_PATH='my-root-group'   # optional
export INCLUDE_FORKS=false                   # optional
export VISIBILITY=private                    # optional: private|internal|public
export DRY_RUN=true                          # zuerst testen
export CONCURRENCY=1                        # optional
export ONLY_REPOS=CyberT33N/ai-sdk           # optional

npx -y tsx /home/t33n/Projects/environments/minikube/scripts/gitlab/mirror/github-to-gitlab.ts
```

## Umgebungsvariablen

- **GITHUB_TOKEN** (erforderlich): GitHub Personal Access Token. Benötigte Scopes: **repo** (priv. Repos) und **read:org** (Org-Listing). Wird für die GitHub API genutzt und für den Import per HTTPS Basic Auth (`x-access-token` als Benutzername, Token als Passwort).
- **GITLAB_TOKEN** (erforderlich): GitLab Personal Access Token mit Scope **api**. Wird verwendet, um Gruppen/Projekte anzulegen und Importe zu starten.
- **GITLAB_BASE_URL** (erforderlich): Basis-URL deiner GitLab-Instanz, z. B. `http://gitlab.minikube.local`. Ohne nachfolgenden Slash.
- **GITLAB_NAMESPACE_PATH** (optional): Ziel-Gruppe in GitLab (z. B. `company` oder `company/dev`). Wenn gesetzt, werden Projekte darunter angelegt; Org-Repos erhalten automatisch passende Untergruppen. Wenn nicht gesetzt, wird dein Benutzer-Namespace verwendet.
- **INCLUDE_FORKS** (optional, Standard: `false`): Wenn `true`, werden Forks mit migriert. Standardmäßig werden Forks übersprungen.
- **VISIBILITY** (optional, Standard: `private`): Sichtbarkeit der neu erstellten Projekte in GitLab. Erlaubt: `private` | `internal` | `public`.
- **DRY_RUN** (optional, Standard: `false`): Wenn `true`, werden keine Projekte erstellt. Stattdessen werden die geplanten Aktionen detailliert geloggt.
- **CONCURRENCY** (optional, Standard: `4`, Bereich: `1`–`20`): Anzahl paralleler Worker für die Migration.

### Weitere optionale Variable
- **GITHUB_USER** (optional): Überschreibt den GitHub-Login, dessen Repositories verarbeitet werden sollen. Standard ist der Benutzer, der durch `GITHUB_TOKEN` authentifiziert ist.

### Hinweise
- Tokens werden nicht in Logs ausgegeben. Die Import-URL setzt den GitHub-Token nur intern für den Import ein.
- Bei GitHub- oder GitLab-Rate-Limits werden automatische **Retry/Backoff**-Strategien genutzt; der Fortschritt wird mit **UTF‑8-Icons** klar geloggt.




github-to-gitlab.ts:
```
/*
Enterprise-grade GitHub -> GitLab mirroring script
Run with: node --experimental-strip-types scripts/gitlab/mirror-github-to-gitlab.ts

Env vars:
  - GITHUB_TOKEN: required, PAT with repo + read:org
  - GITHUB_USER: optional, login whose repos to mirror (defaults to token's user)
  - GITLAB_TOKEN: required, Personal Access Token with api scope
  - GITLAB_BASE_URL: required, e.g. http://gitlab.minikube.local or https://gitlab.example.com
  - GITLAB_NAMESPACE_PATH: optional, group path where to create projects; if absent, use user namespace
  - ONLY_REPO: optional, import only this GitHub repo full name (owner/name)
  - INCLUDE_FORKS: optional, 'true' to include forks (default false)
  - VISIBILITY: optional, one of 'private' | 'internal' | 'public' (default 'private')
  - DRY_RUN: optional, 'true' for no-op
  - CONCURRENCY: optional, integer (default 4)

Behavior:
  - Enumerates GitHub repos for the authenticated user, including orgs the user has access to
  - Creates GitLab groups for GitHub organizations (if a GITLAB_NAMESPACE_PATH is provided, orgs will be nested under it)
  - Creates GitLab projects with import_url set to GitHub HTTPS URL including token for mirroring
  - Skips existing projects (idempotent)
*/

/* eslint-disable no-console */

// Minimal type-safe helpers without external deps

const sleep = (ms: number): Promise<void> => new Promise(res => setTimeout(res, ms));

class HttpError extends Error {
  constructor(message: string, public status: number, public body?: unknown) {
    super(message);
    this.name = 'HttpError';
  }
}

// --- logging helpers ---
const timestamp = (): string => new Date().toISOString();
function logInfo(message: string, details?: unknown): void {
  if (details !== undefined) console.info(`${timestamp()} ℹ️  ${message}`, details);
  else console.info(`${timestamp()} ℹ️  ${message}`);
}
function logWarn(message: string, details?: unknown): void {
  if (details !== undefined) console.warn(`${timestamp()} ⚠️  ${message}`, details);
  else console.warn(`${timestamp()} ⚠️  ${message}`);
}
function logError(message: string, details?: unknown): void {
  if (details !== undefined) console.error(`${timestamp()} ❌ ${message}`, details);
  else console.error(`${timestamp()} ❌ ${message}`);
}

interface GithubRepository {
  id: number;
  name: string;
  full_name: string; // org/name or user/name
  private: boolean;
  fork: boolean;
  archived: boolean;
  owner: {
    login: string;
    type: 'User' | 'Organization';
  };
  clone_url: string; // https URL
  ssh_url: string;
  html_url: string;
  default_branch: string;
  visibility?: 'public' | 'private' | 'internal';
}

interface GitlabGroup {
  id: number;
  full_path: string;
  path: string;
  name: string;
  parent_id: number | null;
}

interface GitlabProject {
  id: number;
  path: string;
  path_with_namespace: string;
  default_branch: string | null;
  ssh_url_to_repo: string;
  http_url_to_repo: string;
  visibility: 'public' | 'private' | 'internal';
}

interface EnvConfig {
  githubToken: string;
  githubUser?: string;
  gitlabToken: string;
  gitlabBaseUrl: string; // without trailing slash ok
  gitlabNamespacePath?: string;
  onlyRepoFullName?: string; // owner/name to import only this repo
  includeForks: boolean;
  visibility: 'private' | 'internal' | 'public';
  dryRun: boolean;
  concurrency: number;
}

function readEnv(): EnvConfig {
  const {
    GITHUB_TOKEN,
    GITHUB_USER,
    GITLAB_TOKEN,
    GITLAB_BASE_URL,
    GITLAB_NAMESPACE_PATH,
    ONLY_REPO,
    INCLUDE_FORKS,
    VISIBILITY,
    DRY_RUN,
    CONCURRENCY,
  } = process.env;

  if (!GITHUB_TOKEN) throw new Error('GITHUB_TOKEN is required');
  if (!GITLAB_TOKEN) throw new Error('GITLAB_TOKEN is required');
  if (!GITLAB_BASE_URL) throw new Error('GITLAB_BASE_URL is required');

  const visibility = (VISIBILITY ?? 'private') as EnvConfig['visibility'];
  if (!['private', 'internal', 'public'].includes(visibility)) {
    throw new Error(`Invalid VISIBILITY: ${VISIBILITY}`);
  }

  const concurrency = Number(CONCURRENCY ?? '4');
  if (!Number.isInteger(concurrency) || concurrency < 1 || concurrency > 20) {
    throw new Error('CONCURRENCY must be an integer between 1 and 20');
  }

  const config: EnvConfig = {
    githubToken: GITHUB_TOKEN,
    githubUser: GITHUB_USER,
    gitlabToken: GITLAB_TOKEN,
    gitlabBaseUrl: GITLAB_BASE_URL.replace(/\/$/, ''),
    gitlabNamespacePath: GITLAB_NAMESPACE_PATH,
    onlyRepoFullName: ONLY_REPO,
    includeForks: (INCLUDE_FORKS ?? 'false').toLowerCase() === 'true',
    visibility,
    dryRun: (DRY_RUN ?? 'false').toLowerCase() === 'true',
    concurrency,
  };
  return config;
}

function parseLinkHeader(link: string | null): Record<string, string> {
  if (!link) return {};
  return link.split(',').reduce<Record<string, string>>((acc, part) => {
    const match = part.match(/<([^>]+)>;\s*rel="([^"]+)"/);
    if (match) {
      const [, url, rel] = match;
      acc[rel] = url;
    }
    return acc;
  }, {});
}

async function fetchJson<T>(input: string, init: RequestInit, retry = 3): Promise<{ data: T; headers: Headers }> {
  for (let attempt = 0; attempt <= retry; attempt++) {
    const res = await fetch(input, init);
    if (res.status >= 200 && res.status < 300) {
      const data = (await res.json()) as T;
      return { data, headers: res.headers };
    }
    if (res.status === 429 || res.status >= 500) {
      const retryAfterSec = Number(res.headers.get('retry-after') ?? '0');
      const backoffSeconds = (retryAfterSec > 0 ? retryAfterSec : Math.min(2 ** attempt, 16));
      logWarn(`🔁 HTTP ${res.status} on ${input}; retrying in ${backoffSeconds}s (attempt ${attempt + 1}/${retry + 1})`);
      await sleep(backoffSeconds * 1000);
      continue;
    }
    let body: unknown;
    try { body = await res.json(); } catch { body = await res.text(); }
    throw new HttpError(`HTTP ${res.status} for ${input}`, res.status, body);
  }
  throw new Error('Unreachable');
}

async function* paginateGithub<T>(url: string, headers: HeadersInit): AsyncGenerator<T, void, unknown> {
  let nextUrl: string | undefined = url;
  while (nextUrl) {
    const { data, headers: responseHeaders } = await fetchJson<T[]>(nextUrl, { headers });
    for (const item of data) yield item as T;
    const links = parseLinkHeader(responseHeaders.get('link'));
    nextUrl = links['next'];
  }
}

async function listAllGithubRepos(token: string, loginOverride?: string): Promise<GithubRepository[]> {
  const headers: HeadersInit = {
    Authorization: `Bearer ${token}`,
    Accept: 'application/vnd.github+json',
    'X-GitHub-Api-Version': '2022-11-28',
  };

  // Determine the authenticated user if not provided
  let targetLogin = loginOverride;
  if (!targetLogin) {
    logInfo('🌐 Resolving GitHub authenticated user');
    const { data } = await fetchJson<{ login: string }>('https://api.github.com/user', { headers });
    targetLogin = data.login;
    logInfo(`👤 GitHub user resolved: ${targetLogin}`);
  }

  const repos: GithubRepository[] = [];

  // User repositories (includes private if token has access)
  logInfo('📄 Fetching user repositories');
  for await (const repo of paginateGithub<GithubRepository>(`https://api.github.com/user/repos?per_page=100&sort=full_name&direction=asc`, headers)) {
    repos.push(repo);
  }

  // Organizations for the user
  logInfo('🗂️ Fetching organizations');
  const orgs: Array<{ login: string }> = [];
  for await (const org of paginateGithub<{ login: string }>('https://api.github.com/user/orgs?per_page=100', headers)) {
    orgs.push(org);
  }
  logInfo(`🗂️ Found ${orgs.length} organizations`);

  // Org repositories per organization
  for (const org of orgs) {
    logInfo(`📦 Fetching repositories for org: ${org.login}`);
    for await (const repo of paginateGithub<GithubRepository>(`https://api.github.com/orgs/${encodeURIComponent(org.login)}/repos?per_page=100&type=all&sort=full_name&direction=asc`, headers)) {
      repos.push(repo);
    }
  }

  // De-duplicate by full_name
  const uniqueMap = new Map<string, GithubRepository>();
  for (const r of repos) uniqueMap.set(r.full_name, r);
  logInfo(`🔎 Discovered ${repos.length} repos (${uniqueMap.size} unique by full_name)`);
  return Array.from(uniqueMap.values());
}

// GitLab helpers
async function gitlabGet<T>(baseUrl: string, token: string, path: string, searchParams?: Record<string, string>): Promise<T> {
  const url = new URL(`${baseUrl.replace(/\/$/, '')}/api/v4${path}`);
  if (searchParams) {
    for (const [k, v] of Object.entries(searchParams)) url.searchParams.set(k, v);
  }
  const { data } = await fetchJson<T>(url.toString(), {
    headers: {
      'Private-Token': token,
      Accept: 'application/json',
    },
  });
  return data;
}

async function gitlabPost<T>(baseUrl: string, token: string, path: string, body: unknown): Promise<T> {
  const url = `${baseUrl.replace(/\/$/, '')}/api/v4${path}`;
  const { data } = await fetchJson<T>(url, {
    method: 'POST',
    headers: {
      'Private-Token': token,
      'Content-Type': 'application/json',
      Accept: 'application/json',
    },
    body: JSON.stringify(body),
  });
  return data;
}

async function gitlabPaginate<T>(baseUrl: string, token: string, path: string, searchParams?: Record<string, string>): Promise<T[]> {
  const url = new URL(`${baseUrl.replace(/\/$/, '')}/api/v4${path}`);
  if (searchParams) for (const [k, v] of Object.entries(searchParams)) url.searchParams.set(k, v);
  const items: T[] = [];
  let page = 1;
  for (;;) {
    url.searchParams.set('per_page', '100');
    url.searchParams.set('page', String(page));
    const { data, headers } = await fetchJson<T[]>(url.toString(), {
      headers: {
        'Private-Token': token,
        Accept: 'application/json',
      },
    });
    for (const it of data) items.push(it);
    const nextPage = headers.get('x-next-page');
    if (!nextPage) break;
    page = Number(nextPage);
  }
  return items;
}

async function ensureGitlabGroup(baseUrl: string, token: string, fullPath: string, parentId?: number): Promise<GitlabGroup> {
  // Try to get by full_path
  try {
    const found = await gitlabGet<GitlabGroup>(baseUrl, token, `/groups/${encodeURIComponent(fullPath)}`);
    logInfo('🗂️ Found existing GitLab group', { fullPath: fullPath, id: found.id });
    return found;
  } catch (e) {
    logWarn('🗂️ GitLab group not found; creating', { fullPath });
  }

  const path = fullPath.split('/').pop() as string;
  const name = path;
  const payload: Record<string, unknown> = { name, path };
  if (parentId) payload['parent_id'] = parentId;

  const created = await gitlabPost<GitlabGroup>(baseUrl, token, '/groups', payload);
  logInfo('🗂️ Created GitLab group', { fullPath: fullPath, id: created.id });
  return created;
}

async function findGitlabNamespace(baseUrl: string, token: string, fullPath: string): Promise<{ id: number; kind: 'group' | 'user' } | null> {
  const namespaces = await gitlabPaginate<{ id: number; full_path: string; kind: 'group' | 'user' }>(baseUrl, token, '/namespaces', { search: fullPath });
  for (const ns of namespaces) {
    if (ns.full_path.toLowerCase() === fullPath.toLowerCase()) return { id: ns.id, kind: ns.kind };
  }
  return null;
}

async function getOrCreateOrgGroup(baseUrl: string, token: string, githubOrg: string, rootNamespacePath?: string): Promise<GitlabGroup | null> {
  if (!rootNamespacePath) return null;

  // Ensure root group exists
  const rootNs = await findGitlabNamespace(baseUrl, token, rootNamespacePath);
  let rootGroup: GitlabGroup | null = null;
  if (!rootNs) {
    logWarn('🗂️ Root namespace not found; creating', { rootNamespacePath });
    rootGroup = await ensureGitlabGroup(baseUrl, token, rootNamespacePath);
  } else if (rootNs.kind === 'group') {
    rootGroup = await gitlabGet<GitlabGroup>(baseUrl, token, `/groups/${encodeURIComponent(rootNamespacePath)}`);
    logInfo('🗂️ Using existing root group', { rootNamespacePath, id: rootGroup.id });
  }

  if (!rootGroup) return null;

  // Ensure org subgroup under root
  const fullPath = `${rootGroup.full_path}/${githubOrg}`;
  try {
    const existing = await gitlabGet<GitlabGroup>(baseUrl, token, `/groups/${encodeURIComponent(fullPath)}`);
    logInfo('🗂️ Using existing org subgroup', { fullPath, id: existing.id });
    return existing;
  } catch {
    const created = await gitlabPost<GitlabGroup>(baseUrl, token, '/groups', {
      name: githubOrg,
      path: githubOrg,
      parent_id: rootGroup.id,
    });
    logInfo('🗂️ Created org subgroup', { fullPath, id: created.id });
    return created;
  }
}

async function findExistingProject(baseUrl: string, token: string, pathWithNamespace: string): Promise<GitlabProject | null> {
  try {
    return await gitlabGet<GitlabProject>(baseUrl, token, `/projects/${encodeURIComponent(pathWithNamespace)}`);
  } catch {
    return null;
  }
}

function buildGithubHttpsUrlWithToken(repoHttpsUrl: string, githubToken: string): string {
  // Convert https://github.com/owner/name.git to include token using basic auth username/password
  // Use username 'x-access-token' and token as password, per GitHub guidance
  const url = new URL(repoHttpsUrl);
  url.username = 'x-access-token';
  url.password = githubToken;
  return url.toString();
}

function normalizeVisibility(vis: EnvConfig['visibility']): 'private' | 'internal' | 'public' {
  if (vis === 'public' || vis === 'private' || vis === 'internal') return vis;
  return 'private';
}

function toProjectPath(name: string): string {
  // Sanitize to GitLab path rules: letters, digits, '_', '-', '.'
  const lower = name.trim().toLowerCase();
  const replaced = lower.replace(/\s+/g, '-');
  const cleaned = replaced.replace(/[^a-z0-9_.-]/g, '-').replace(/-+/g, '-').replace(/^[-.]+|[-.]+$/g, '');
  return cleaned.length > 0 ? cleaned : 'repo';
}

async function createGitlabProjectWithImport(baseUrl: string, token: string, params: {
  name: string;
  path: string;
  namespaceId?: number;
  importUrl: string;
  visibility: EnvConfig['visibility'];
  defaultBranch?: string;
}): Promise<GitlabProject> {
  const payload: Record<string, unknown> = {
    // Use sanitized path as display name to avoid validation errors for names starting/ending with special chars
    name: params.path,
    path: params.path,
    visibility: normalizeVisibility(params.visibility),
    import_url: params.importUrl,
  };
  if (params.namespaceId) payload['namespace_id'] = params.namespaceId;
  if (params.defaultBranch) payload['default_branch'] = params.defaultBranch;

  logInfo('📦 Creating GitLab project', { path: params.path, namespaceId: params.namespaceId ?? '(user namespace)' });
  return await gitlabPost<GitlabProject>(baseUrl, token, '/projects', payload);
}

async function run(): Promise<void> {
  const cfg = readEnv();
  logInfo('🚀 Starting GitHub → GitLab mirroring', {
    gitlabBaseUrl: cfg.gitlabBaseUrl,
    gitlabNamespacePath: cfg.gitlabNamespacePath ?? '(user namespace)',
    includeForks: cfg.includeForks,
    visibility: cfg.visibility,
    dryRun: cfg.dryRun,
    concurrency: cfg.concurrency,
  });

  const allRepos = await listAllGithubRepos(cfg.githubToken, cfg.githubUser);
  // Optional: restrict to a single repo by full name (owner/name)
  const selection = cfg.onlyRepoFullName
    ? allRepos.filter(r => r.full_name.toLowerCase() === cfg.onlyRepoFullName!.toLowerCase())
    : allRepos;
  const filtered = selection.filter(r => !r.archived && (cfg.includeForks || !r.fork));
  const skipped = allRepos.length - filtered.length;

  if (cfg.onlyRepoFullName) {
    logInfo(`🔎 Discovered ${allRepos.length} repos; restricted to ONLY_REPO=${cfg.onlyRepoFullName}; processing ${filtered.length} after filtering`);
  } else {
    logInfo(`🔎 Discovered ${allRepos.length} repos; processing ${filtered.length} after filtering (${skipped} skipped)`);
  }

  // Prepare GitLab namespace roots
  const baseNamespace = cfg.gitlabNamespacePath ? await findGitlabNamespace(cfg.gitlabBaseUrl, cfg.gitlabToken, cfg.gitlabNamespacePath) : null;
  let baseNamespaceId: number | undefined = undefined;
  if (baseNamespace && baseNamespace.kind === 'group') baseNamespaceId = baseNamespace.id;

  // Resolve current GitLab user to build default path when no namespace is provided
  const currentUser = await gitlabGet<{ username: string }>(cfg.gitlabBaseUrl, cfg.gitlabToken, '/user');
  if (cfg.gitlabNamespacePath) {
    if (baseNamespaceId) logInfo('🧭 Base group resolved', { path: cfg.gitlabNamespacePath, id: baseNamespaceId });
    else logWarn('🧭 Base group not found; will be created on demand', { path: cfg.gitlabNamespacePath });
  } else {
    logInfo('🧭 Using GitLab user namespace', { username: currentUser.username });
  }

  // Concurrency control
  const queue = [...filtered];
  const total = queue.length;
  let active = 0;
  let done = 0;
  const failures: Array<{ repo: string; error: unknown }> = [];

  logInfo('🧵 Starting workers', { workers: cfg.concurrency, total });

  async function worker(workerId: number): Promise<void> {
    while (queue.length > 0) {
      const repo = queue.shift();
      if (!repo) break;
      active++;
      const orgOrUser = repo.owner.login;
      const isOrg = repo.owner.type === 'Organization';
      const targetName = repo.name;
      const targetPath = toProjectPath(repo.name);

      try {
        logInfo(`🔧 [#${workerId}] Processing ${repo.full_name}`);
        // Determine namespace
        let namespaceIdToUse: number | undefined = baseNamespaceId;
        let namespacePathStr: string | undefined = cfg.gitlabNamespacePath;

        if (isOrg) {
          const orgGroup = await getOrCreateOrgGroup(cfg.gitlabBaseUrl, cfg.gitlabToken, orgOrUser, cfg.gitlabNamespacePath);
          if (orgGroup) {
            namespaceIdToUse = orgGroup.id;
            namespacePathStr = orgGroup.full_path;
          }
        }

        const effectiveNamespacePath = namespacePathStr ?? currentUser.username;
        const pathWithNamespace = `${effectiveNamespacePath}/${targetPath}`;

        const existing = await findExistingProject(cfg.gitlabBaseUrl, cfg.gitlabToken, pathWithNamespace);
        if (existing) {
          logInfo(`⏭️  Exists, skipping: ${existing.path_with_namespace}`);
          done++;
          active--;
          continue;
        }

        const importUrl = buildGithubHttpsUrlWithToken(repo.clone_url, cfg.githubToken);
        const payload = {
          name: targetName,
          path: targetPath,
          namespaceId: namespaceIdToUse,
          importUrl,
          visibility: cfg.visibility,
          defaultBranch: repo.default_branch,
        } as const;

        if (cfg.dryRun) {
          logInfo(`📝 [dry-run] Would create: ${effectiveNamespacePath}/${targetPath} ← ${repo.full_name}`);
        } else {
          const created = await createGitlabProjectWithImport(cfg.gitlabBaseUrl, cfg.gitlabToken, payload);
          logInfo(`✅ Created: ${created.path_with_namespace} (import scheduled)`);
        }
        done++;
        logInfo(`📈 Progress: ${done}/${total}`);
      } catch (error) {
        failures.push({ repo: repo.full_name, error });
        logWarn(`❗ [#${workerId}] Error processing ${repo.full_name}`, error);
      } finally {
        active--;
      }
    }
  }

  const workers = Array.from({ length: cfg.concurrency }, (_, i) => worker(i + 1));
  await Promise.all(workers);

  logInfo(`🏁 Completed. Success: ${done - failures.length}, Failed: ${failures.length}`);
  if (failures.length > 0) {
    for (const f of failures) logWarn('• Failure', { repo: f.repo, error: f.error });
    process.exitCode = 1;
  }
}

run().catch(err => {
  logError('💥 Fatal error', err);
  process.exit(1);
});
```
