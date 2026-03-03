# Git LFS

#### Upload/Commit bigger files than 100mb (https://git-lfs.github.com/)
```bash
# Download (https://github.com/git-lfs/git-lfs/releases/latest) and install the Git command line extension. Once downloaded and installed, set up Git LFS for your user account by running:
git lfs install

#In each Git repository where you want to use Git LFS, select the file types you'd like Git LFS to manage (or directly edit your .gitattributes). You can configure additional file extensions at anytime.
git lfs track "*.psd"

# Now make sure .gitattributes is tracked:
git add .gitattributes

# Note that defining the file types Git LFS should track will not, by itself, convert any pre-existing files to Git LFS, such as files on other branches or in your prior commit history. To do that, use the git lfs migrate[1] command, which has a range of options designed to suit various potential use cases.

# There is no step three. Just commit and push to GitHub as you normally would; for instance, if your current branch is named main:
git add file.psd
git commit -m "Add design file"
git push origin main
```

#### Uninstall from repo
```bash
git lfs uninstall
```






<br><br>

---

<br><br>


## Check what LFS would track
```shell
git lfs track
git lfs ls-files
```










<br><br>

---

<br><br>


# Troubleshooting




## Upload speed
- Bitte prüfen, ob das VPN aktiv ist. Das VPN kann es auf jeden Fall stark reduzieren.

Zusätzlich kann man unten noch die maximale Anzahl der Konkurrenten erhöhen.

```shell
    git config lfs.concurrenttransfers 8
```


















<br><br>
<br><br>



### Bitbucket push blocked (file ≥ 100 MB) even though `.gitattributes` looks correct

#### Symptom

Your push is rejected with a Bitbucket pre-receive hook message similar to:

```text
Your push has been blocked because it includes at least one file that is 100 MB or larger.
... (pre-receive hook declined)
```

#### What it means (it is *not* the 4 GB limit)

This error is the **single-file size gate** (≥ 100 MB) for **non-LFS Git blobs**.  
It is **not** a “repository is 4 GB full” problem.

#### Why it can still happen when you “already added it to `.gitattributes`”

- **`.gitattributes` is not retroactive**: if a large file was committed before it was tracked by Git LFS, it remains a normal Git blob in history and still blocks the push.
- **Path mismatch**: your LFS rule is path-scoped, but the actual file path is outside that scope.
- **Rule not committed**: if `.gitattributes` changes were not committed before the large file was added, the large file can end up as a normal Git blob.

#### 1) Find the offending blobs (exact paths)

First, get an overview of large files in a specific branch history:

```bash
git lfs migrate info --include-ref=refs/heads/<your-branch> --above=100MB
```

Then list the **exact file paths** for all blobs ≥ 100 MB (PowerShell):

```powershell
git rev-list --objects --all |
git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' |
Select-String '^blob ' |
ForEach-Object {
  $p = $_.Line.Split(' ', 4)
  [pscustomobject]@{ Oid=$p[1]; SizeMB=[math]::Round(([int64]$p[2]/1MB),2); Path=$p[3] }
} |
Where-Object { $_.SizeMB -ge 100 } |
Sort-Object SizeMB -Descending |
Format-Table -AutoSize -Wrap
```

#### 2) Verify whether `.gitattributes` actually matches a path

For a suspicious path from the list, check whether Git would apply LFS attributes:

```bash
git check-attr filter diff merge text -- "<path/from/the-list>"
```

You want to see `filter: lfs` for paths that must go to LFS.

#### 3) Add the correct LFS rules (then commit)

- Prefer **path-scoped** rules (more precise than “track all `*.sql` everywhere”).
- In `.gitattributes`, always use **forward slashes** even on Windows.

Example (adapt to your repo layout):

```gitattributes
# Large data artifacts via Git LFS (path-scoped)
# Note: Use forward slashes even on Windows
/data/dumps/pvs/** filter=lfs diff=lfs merge=lfs -text
/data/reference/pvs/z1/**/sql/** filter=lfs diff=lfs merge=lfs -text
```

Commit the rules:

```bash
git add .gitattributes
git commit -m "chore(lfs): track large artifacts via git lfs"
```

#### 4) Migrate existing branch history into LFS (required for already-committed large files)

Because `.gitattributes` is not retroactive, you must rewrite the feature-branch history so the old large blobs become LFS pointers.

```bash
# Safety net (local)
git branch backup/pre-lfs-migrate

# Ensure LFS is enabled
git lfs install

# Rewrite THIS branch ref: convert matching paths to LFS pointers
git lfs migrate import \
  --include-ref=refs/heads/<your-branch> \
  --include="data/dumps/pvs/**,data/reference/pvs/z1/**/sql/**"
```

Verify again:

```bash
git lfs migrate info --include-ref=refs/heads/<your-branch> --above=100MB
```

#### 5) Push (history was rewritten)

After migration, pushing requires a force push (safe variant):

```bash
git push --force-with-lease -u origin HEAD
```

#### If it still fails

- There is **another ≥ 100 MB blob** outside your tracked patterns.
- Repeat steps 1–2, add/adjust `.gitattributes` rules to cover the missing path(s), then rerun step 4.
