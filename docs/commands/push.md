# push (https://git-scm.com/docs/git-push)
- Update your repo based on your commits
```bash
git push
```

## fatal: You are not currently on a branch. To push the history leading to the current (detached HEAD)
```bash
git push origin HEAD:branchname --force
```

## Push to specific remote repo
```bash
git push REMOTE_REPO_NAME BRANCH
```

## Push to all remote repos
```bash
git remote | xargs -L1 git push --all
```