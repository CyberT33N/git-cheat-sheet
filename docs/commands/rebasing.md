# rebase
- does same thing like merge

## rebase master branch into feature branch
```bash
git checkout feature
git commit -m "any cool changes"

git checkout master
git push

git checkout feaure
git rebase master

git push
```

## interactive rebasing
```bash
git checkout feature
git rebase -i master
```

## Squash merge (rebase) without conflicts
```bash
git fetch -u origin develop:develop
git rebase develop
# Resolve conflicts: git add . && git rebase --continue
git push -f
```