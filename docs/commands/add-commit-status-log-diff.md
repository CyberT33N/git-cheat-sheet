# status (https://git-scm.com/docs/git-status)
- display state of project (current branch and more..)
- display changes
```bash
git status
```

# add

## add all files in folder recursive
- You should always add each file manually to prevent mistakes. Only use this command when you are 100% sure.
```bash
git add .
# add parent folder
git add ..
```

# commit

## Commit Message Conventions
- https://github.com/angular/angular.js/blob/master/DEVELOPERS.md#-git-commit-guidelines
- https://gist.github.com/stephenparish/9941e89d80e2bc58a153
```bash
feat (feature)
fix (bug fix)
docs (documentation)
style (formatting, missing semi colons, …)
refactor
test (when adding missing tests)
chore (maintain)
```

## commit all changes from all files
```bash
git add .
git commit -m "TITLE" -m "DESCRIPTION"
```

## commit specific file
```bash
git add app.js
git commit -m "TITLE" -m "DESCRIPTION"
```

## empty commit
```bash
git commit -a --allow-empty --allow-empty-message -m ''
```

## overwrite/delete last commit (amend)
```bash
git add .
git commit --amend -m "an updated commit message"
git push -f
# Skip commit message dialog by using message from last commit
git add . && git commit --amend --reuse-message HEAD && git push -f
```

# Log

## Show logs of last commits
```bash
git log
# beautify
git log --all --graph --decorate --oneline
```

## Show all commit hashes/id
```bash
git log --pretty=format:"%h"
# Specific branch
git log main..feat/CCS-1114/new-data-structure/main --pretty=format:"%h"
# Specific branch (oneline)
git log main..feat/CCS-1114/new-data-structure/main --oneline
```

# diff

## exclude specific file
```bash
git diff -- . ':!package-lock.json'
```