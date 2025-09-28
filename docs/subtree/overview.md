# Git Subtree Management für `extensions/roo-code`

## Initiales Hinzufügen eines bestehenden Ordners als Subtree
```bash
git remote add -f roo-code-upstream https://github.com/RooVetGit/Roo-Code.git
mv extensions/roo-code extensions/roo-code_TEMP
git subtree add --prefix=extensions/roo-code roo-code-upstream main --squash
rsync -a --delete extensions/roo-code_TEMP/ extensions/roo-code/
git add extensions/roo-code
git commit -m "Merge local roo-code changes after subtree add"
git rm -r extensions/roo-code_TEMP
git commit -m "Remove temporary roo-code directory"
```

## Workflow zum Aktualisieren des Subtrees (Pull)
```bash
git subtree pull --prefix=extensions/roo-code roo-code-upstream main --squash
```
Bei Konflikten betroffene Dateien lösen, `git add` und `git commit` ausführen.
