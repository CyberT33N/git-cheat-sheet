# fetch (https://git-scm.com/docs/git-fetch)

## difference between fetch and pull
- https://stackoverflow.com/questions/292357/what-is-the-difference-between-git-pull-and-git-fetch
- pull does a git fetch followed by a git merge
- You can do a git fetch at any time to update your remote-tracking branches under refs/remotes/<remote>/. But your local files fill NOT update

## Force pull by overwriting local files
```bash
git fetch --all
# if you are on the master branch
git reset --hard origin/master
# if other branch
# git reset --hard origin/<branch_name>
```

## Fetch from specific remote repo
```bash
git fetch -u origin localbranch:remotebranch
```