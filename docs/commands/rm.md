


# Remove node_modules folder remote

Für den sicheren Standardfall, also `node_modules` aus dem aktuellen Stand von `origin/main` entfernen, aber lokal behalten:

```bash
git rm -r --cached node_modules
printf '\nnode_modules/\n' >> .gitignore
git add .gitignore
git commit -m "chore: remove node_modules from repository"
git push origin main
```

Wichtige Unterscheidung: Das löscht `node_modules` sauber aus dem aktuellen Remote-Branch-Zustand, aber nicht aus der bereits veröffentlichten Historie. Wenn du ihn aus der kompletten Remote-Historie entfernen willst, ist das ein History-Rewrite und damit ein anderer, riskanterer Vorgang. In dem Fall kann ich dir den korrekten `git filter-repo`-Workflow dafür geben.
