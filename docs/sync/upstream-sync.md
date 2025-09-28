# Lokales Repository erstmalig mit Upstream synchronisieren (bei nicht verwandten Historien)

**Szenario:**

Du hast ein lokales Git-Repository, das ursprünglich von einem anderen Repository (z. B. einem Klon oder Fork) stammt, aber dessen Commit-Historie sich inzwischen von der des ursprünglichen "Upstream"-Repositories unterscheidet. Du möchtest dein lokales Repository nun auf den Stand des Upstream-Repositories bringen, dabei aber deine eigenen, spezifischen Änderungen (z. B. hinzugefügte Dateien/Ordner) beibehalten und eine saubere, gemeinsame Historie für die Zukunft schaffen.

**Problem:**

Ein direkter `git merge` oder `git pull` vom Upstream führt wahrscheinlich zu einem `fatal: refusing to merge unrelated histories`-Fehler oder zu massiven Merge-Konflikten, da Git die beiden unterschiedlichen Historien nicht automatisch zusammenführen kann.

**Lösungsweg (Wie wir es gemacht haben):**

Ziel ist es, die Historie des Upstream-Repositories als Basis zu übernehmen und deine spezifischen Änderungen als neue Commits *darauf* anzuwenden.

1. **Upstream-Remote hinzufügen (falls noch nicht geschehen):**
```bash
git remote add upstream <upstream-url>
```
2. **Upstream-Änderungen holen:**
```bash
git fetch upstream
```
3. **Aktuellen Stand sichern (WICHTIG):**
```bash
git branch main_backup
```
4. **Lokalen Branch auf Upstream zurücksetzen:**
```bash
git checkout main
git reset --hard upstream/main
```
5. **Eigene Änderungen identifizieren:**
```bash
git diff --name-status main_backup main
```
6. **Eigene Änderungen selektiv wiederherstellen:**
```bash
git checkout main_backup -- <files-to-keep>
```
7. **Eigene Änderungen committen:**
```bash
git add <files-to-keep>
git commit -m "feat: Add custom files and configurations"
```
8. **Lokalen Branch zum eigenen Remote pushen (Force Push):**
```bash
git push --force-with-lease origin main
```
9. **Aufräumen (Optional):**
```bash
git branch -d main_backup
```

---

## Regelmäßiger Workflow: Upstream-Änderungen in lokalen Branch mergen

1. `git fetch upstream`
2. `git checkout main`
3. `git merge upstream/main`
4. Konflikte lösen und committen
5. `git push origin main`
