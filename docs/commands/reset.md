# reset
- **--soft** will be used by default when you use git reset. Use --soft if you want to keep your changes
- Use **--hard** if you don't care about keeping the changes you made

## reset last head commit
```bash
git checkout <branch-to-modify-head>
git reset --soft HEAD^
git reset --hard HEAD^
git push -f
```

## replace last head commit with another commit
```bash
git checkout <branch-to-modify-head>
git reset --hard <commit-hash-id-to-put-as-head>
git push -f
```

## Remove specific amount of head commits but keep local changes
```bash
git checkout dein_branch
git reset --soft HEAD~4
# verify with git log / git status
git add .
# verify staged files
git status
# force push
git push -f
```

## Reset last merge
```bash
rm -rf .git/MERGE*
```

## Cancel current merge process
```bash
git reset --hard HEAD
git clean -d -f 
```

## Set master branch into main
```bash
git checkout main
git reset --hard master
git push -f origin main
```