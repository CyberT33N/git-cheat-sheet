# Branch

## get current Branch
```
git rev-parse --abbrev-ref HEAD
# git pull origin $(git rev-parse --abbrev-ref HEAD)
```

## Show all branches local and remote
```bash
git branch -av
```

## Get default branch
```bash
git remote show <remote_name> | awk '/HEAD branch/ {print $NF}'
```

## Check all branches
```bash
git branch
```

## Check current branch 
```bash
git branch --show-current
```

## Create branch
```bash
git branch branch_name
```

## delete branch
```bash
# delete branch locally
git branch -d localBranchName
# delete branch remotely
git push origin --delete remoteBranchName
```

## rename branch
```bash
git branch -m "old-branch-name-here" "new-branch-name-here"
```

## develop, feature and feature-dev branch
```
# Pull from latest main branch in this case develop and then create feature branch
git checkout develop
git pull
git branch fix/TICKET-232/edit-custom-block/main

# Create feature-dev branch, checkout to it and then work on your ticket
git checkout fix/TICKET-232/edit-custom-block/main
git branch fix/TICKET-232/edit-custom-block/kho
git checkout fix/CCS-232/edit-custom-block/kho

# Merge feature-dev branch into feature branch
git checkout fix/TICKET-232/edit-custom-block/main
git merge --squash fix/TICKET-232/edit-custom-block/kho
git commit -m "fix(TICKET-232): Edit template"

# Rebase develop branch on feature branch
git fetch -u origin develop:develop
git rebase develop

# Push feature branch
git push --force --set-upstream origin fix/TICKET-232/edit-custom-block/main

# Create MR/PR
```