# revert (https://www.atlassian.com/git/tutorials/undoing-changes/git-revert)
- The git revert command can be considered an 'undo' type command, however, it is not a traditional undo operation. Instead of removing the commit from the project history, it figures out how to invert the changes introduced by the commit and appends a new commit with the resulting inverse content.

## revert specific commit
```bash
git revert id_here
```

## abort revert operation
```bash
git revert --abort
```