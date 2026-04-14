## Thematik — Dateien wieder aus Git LFS entfernen

Wenn eine Datei versehentlich über **Git LFS** getrackt wurde, reicht es **nicht**, nur die LFS-Regel in [`.gitattributes`](.gitattributes) zu ändern.

Zusätzlich muss die Datei:
- aus dem Git-Index entfernt werden,
- danach erneut normal hinzugefügt werden,
- und anschließend neu committed werden.

Der sichere Standardpfad ist also immer:
1. **LFS-Tracking-Regel korrigieren**
2. **Datei oder Dateimenge aus dem Index entfernen**
3. **Datei oder Dateimenge normal neu adden**
4. **committen und pushen**

Wichtig:
- Dieser Ablauf entfernt Git LFS **für den aktuellen und zukünftigen Stand**.
- Alte Commits in der Historie bleiben dabei unverändert.
- Für bereits gepushte Branches ist das der **sichere Standard ohne History-Rewrite**.

---

## Beispiel 1 — Einzelne Datei aus Git LFS entfernen

### Ziel
Eine einzelne Datei soll nicht mehr über Git LFS laufen, sondern wieder als normale Git-Datei gespeichert werden.

### Beispiel für [`.gitattributes`](.gitattributes)

Falls eine breite LFS-Regel existiert, kann eine gezielte Ausnahme für genau eine Datei gesetzt werden:

```gitattributes
/data/dumps/pvs/** filter=lfs diff=lfs merge=lfs -text
/data/dumps/pvs/z1/base/sql/without-timer/all-in-one/psi.md -filter -diff -merge text eol=lf
```

### Git-Befehle

```bash
git switch <branch>
git pull --ff-only
git rm --cached -- data/dumps/pvs/z1/base/sql/without-timer/all-in-one/psi.md
git add .gitattributes
git add data/dumps/pvs/z1/base/sql/without-timer/all-in-one/psi.md
git commit -m "Remove Git LFS tracking from psi.md"
git push origin HEAD
```

### Prüfung

```bash
git check-attr filter diff merge -- data/dumps/pvs/z1/base/sql/without-timer/all-in-one/psi.md
git show HEAD:data/dumps/pvs/z1/base/sql/without-timer/all-in-one/psi.md
```

### Erwartung
- `filter=lfs` darf nicht mehr aktiv sein.
- `git show` muss den echten Dateiinhalt zeigen und **nicht** den Git-LFS-Pointer.

---

## Beispiel 2 — Alle Markdown-Dateien in einem spezifischen Ordner aus Git LFS entfernen

### Ziel
Alle Markdown-Dateien in einem bestimmten Ordner sollen wieder normale Git-Dateien werden.

### Beispiel für [`.gitattributes`](.gitattributes)

Wenn alle Markdown-Dateien in einem bestimmten Ordnerbereich aus Git LFS herausgenommen werden sollen, kann eine gezielte Ausnahme für diesen Ordner gesetzt werden:

```gitattributes
/data/dumps/pvs/** filter=lfs diff=lfs merge=lfs -text
/data/dumps/pvs/**/*.md -filter -diff -merge text eol=lf
```

### Git-Befehle

```bash
git switch <branch>
git pull --ff-only
git rm --cached -- ":(glob)data/dumps/pvs/**/*.md"
git add .gitattributes
git add -- ":(glob)data/dumps/pvs/**/*.md"
git commit -m "Remove Git LFS tracking from Markdown files in data/dumps/pvs"
git push origin HEAD
```

### Prüfung

```bash
git check-attr filter diff merge -- data/dumps/pvs/z1/base/sql/without-timer/all-in-one/psi.md
git ls-files -- ":(glob)data/dumps/pvs/**/*.md"
```

### Erwartung
- Markdown-Dateien unter `data/dumps/pvs` werden nicht mehr über Git LFS getrackt.
- Die Dateien liegen wieder als normale Textdateien im Git-Stand vor.

---

## Merksatz für das Cheat Sheet

**Git LFS entfernen = `.gitattributes` korrigieren + Dateien mit `git rm --cached` aus dem Index lösen + normal neu adden + committen.**

---

## Abgrenzung

Dieser Ablauf ist **kein** History-Rewrite.

Wenn Dateien auch aus **alten Commits** rückwirkend aus Git LFS entfernt werden sollen, ist das ein separater, deutlich riskanter Vorgang mit History-Umschreibung.
