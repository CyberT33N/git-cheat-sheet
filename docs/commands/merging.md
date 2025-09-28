# merge

## Merge Request
- You can request merge request via Dashboard of Gitlab. In GitHub it is called pull request but means the same.

## Merge Conflict
- After resolve merge conflict use **git commit**

## Ignore merge conflicts
```bash
rm -rf .git/MERGE*
```

## merge master branch into feature branch
```bash
git checkout feature
git merge master
# or in 1 line
git checkout feature git merge master
# or in short
git merge feature master
```