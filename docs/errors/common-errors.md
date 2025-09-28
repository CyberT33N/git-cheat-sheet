# Error

## fatal: in unpopulated submodule
```bash
git rm --cached foldernamehere -f
```

## You have divergent branches and need to specify how to reconcile them
```bash
git pull --ff-only
# If you get error fatal: Not possible to fast-forward, aborting
git pull --rebase
```