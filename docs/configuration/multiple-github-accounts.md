# Multiple GitHub Accounts

Use this setup when one machine must access different repositories with different GitHub accounts.

## Goal
- Repository A should authenticate as GitHub account A.
- Repository B should authenticate as GitHub account B.
- Each repository should use its own local commit identity.

## Recommended approach
- Create one SSH key pair per GitHub account.
- Add each public key to the matching GitHub account.
- Create one SSH host alias per account in `~/.ssh/config`.
- Clone each repository with the matching alias.
- Set `user.name` and `user.email` locally inside each repository.

## 1. Create one SSH key per account
```bash
ssh-keygen -t ed25519 -C "account-a@example.com" -f "$env:USERPROFILE\.ssh\github-account-a"
ssh-keygen -t ed25519 -C "account-b@example.com" -f "$env:USERPROFILE\.ssh\github-account-b"
```

This creates:
- `github-account-a` and `github-account-a.pub`
- `github-account-b` and `github-account-b.pub`

Use a different file name for each account. Never reuse the same SSH key across multiple GitHub accounts.

## 2. Start the SSH agent and load both keys
```bash
Get-Service ssh-agent | Set-Service -StartupType Automatic
Start-Service ssh-agent
ssh-add "$env:USERPROFILE\.ssh\github-account-a"
ssh-add "$env:USERPROFILE\.ssh\github-account-b"
ssh-add -l
```

## 3. Add each public key to the correct GitHub account
Open the matching `.pub` file and add it to the matching GitHub account:
- GitHub: <https://github.com/settings/keys>

Required mapping:
- `github-account-a.pub` -> GitHub account A
- `github-account-b.pub` -> GitHub account B

## 4. Create SSH host aliases
Create or update `~/.ssh/config`:

```sshconfig
Host github-account-a
    HostName github.com
    User git
    IdentityFile C:\Users\your-user\.ssh\github-account-a
    IdentitiesOnly yes

Host github-account-b
    HostName github.com
    User git
    IdentityFile C:\Users\your-user\.ssh\github-account-b
    IdentitiesOnly yes
```

Replace `your-user` with your Windows username.

You can use any alias names you want. For example, `github-nelly` is valid if that alias points to the correct key.

## 5. Test each alias
```bash
ssh -T git@github-account-a
ssh -T git@github-account-b
```

Expected result:
```text
Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
```

If the returned GitHub username is wrong, stop and fix the alias before cloning any repository.

## 6. Clone the repository with the matching alias
Do not clone with `git@github.com:owner/repo.git` when you use multiple accounts.

Use the alias instead:

```bash
git clone git@github-account-a:owner/repository-a.git
git clone git@github-account-b:owner/repository-b.git
```

Example with a custom alias:

```bash
git clone git@github-nelly:nelly-solutions/privadent-mono.git
```

## 7. Set the commit identity inside each repository
Run these commands inside the cloned repository:

```bash
git config user.name "Account A Name"
git config user.email "account-a@example.com"
```

Use repository-local configuration so that each repository keeps its own author identity.

Verify the active values:

```bash
git config --local --get user.name
git config --local --get user.email
git remote -v
```

## 8. Update an existing repository
If the repository already exists locally, only update the remote URL and the local commit identity.

```bash
git remote set-url origin git@github-account-a:owner/repository-a.git
git config user.name "Account A Name"
git config user.email "account-a@example.com"
```

## Troubleshooting

### Permission denied (publickey)
This usually means one of the following:
- The repository was cloned with `github.com` instead of the configured alias.
- The wrong SSH key was offered.
- The SSH key was not added to the correct GitHub account.
- The GitHub account does not have access to the repository.

### Repository access vs. GitHub authentication
Successful SSH authentication does not automatically grant access to every private repository.

GitHub authentication confirms which account you are.

Repository authorization confirms whether that account can access the repository.

## Organization SSO and Google-backed SSO
Some GitHub organizations enforce SAML SSO. In many companies, the identity provider behind that SSO is Google Workspace or another corporate login provider.

If you see an error like this:

```text
ERROR: The '<organization>' organization has enabled or enforced SAML SSO.
To access this repository, you must use the HTTPS remote with a personal access token or SSH with an SSH key and passphrase that has been authorized for this organization.
```

then the SSH key is valid for GitHub, but it is not yet authorized for that organization.

### Authorize the SSH key for the organization
1. Sign in to GitHub with the correct account.
2. Open `Settings` -> `SSH and GPG keys`.
3. Locate the SSH key that belongs to the account alias.
4. Click `Configure SSO`, `Enable SSO`, or `Authorize`.
5. Select the organization and complete the SSO authorization flow.

If your company uses Google Workspace as the SSO identity provider, GitHub may redirect you through the Google sign-in flow during this authorization step.

After authorization, retry:

```bash
ssh -T git@github-account-a
git clone git@github-account-a:owner/private-repo.git
```

### If SSO authorization is still blocked
- Confirm that the GitHub account is a member of the organization or team.
- Confirm that the account has access to the repository.
- Confirm that the correct SSH key was authorized.
- Ask the organization owner or administrator whether additional SSO approval is required.

### HTTPS fallback
If the organization requires it, use HTTPS with a personal access token:

```bash
git clone https://github.com/owner/private-repo.git
```

## Quick checklist
- One SSH key per GitHub account
- One SSH alias per GitHub account
- Clone with the alias, not with `github.com`
- Set `user.name` and `user.email` locally per repository
- Authorize the SSH key for the organization when SAML SSO is enforced
