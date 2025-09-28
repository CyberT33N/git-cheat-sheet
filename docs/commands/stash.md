# stash (https://git-scm.com/docs/git-stash)
- Record current state of the working directory and index, then go back to a clean working directory.
```bash
git stash
```

## show all stashes
```bash
git stash show
```

## list your stashed changes
```bash
git stash list
```

## create new branch from stash
```bash
1. git stash
2. git stash list
3. git stash branch my_feature_branch stash@{0}
```

## Difference between pop & apply
- `git stash pop` removes the stash after applying, whereas `git stash apply` keeps it.

## apply most recent stash
```bash
git stash apply
```

## apply most recent stash for specific file
```bash
git restore -s "stash@{0}" -- TODO.md   
```

## apply older stash
```bash
git stash apply stash@{n}
```

## apply stash and drop it (pop)
```bash
git stash pop
```

## go back to latest local state after you did pop/apply
```bash
git reset --hard HEAD^
```

## drop a single stash entry
```bash
git stash drop
```