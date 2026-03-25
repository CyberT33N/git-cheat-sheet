# Git LFS Push Error Large File Fix

## Problem
Push auf GitHub schlägt fehl mit Fehlern wie:

- `File ... is 317.61 MB; this exceeds GitHub's file size limit of 100.00 MB`
- `GH001: Large files detected`
- `pre-receive hook declined`

## Ursache
Die Datei liegt zwar unter einem Pfad, der in `.gitattributes` fuer Git LFS eingetragen ist, wurde aber bereits zuvor als normales Git-Objekt in die Historie aufgenommen.

Wichtig:
- `.gitattributes` wirkt nicht rueckwirkend.
- Git LFS greift nur korrekt, wenn die Datei nach aktiver LFS-Konfiguration neu in den Index bzw. in die Historie kommt.
- GitHub prueft beim Push den echten Inhalt der Commits. Wenn dort noch ein normaler Blob groesser als 100 MB steckt, wird der Push abgelehnt.

## Typische Gruende
- Die LFS-Regel wurde erst nach `git add` oder nach dem Commit hinzugefuegt.
- `git lfs install` war lokal noch nicht aktiv.
- Die Datei wurde nach der Regel-Aenderung nicht erneut korrekt als LFS-Datei aufgenommen.
- In einem frueheren Commit des Branches steckt noch der grosse normale Blob.

## Schnellpruefung
Pruefen, ob die Regel heute passt:

```powershell
git check-attr filter diff merge -- "apps/privyou/data/pms/dumps/dampsoft/production/practices/peschel/DS/daten/PATINFO.csv"
```

Pruefen, ob die Datei aktuell als LFS-Datei gefuehrt wird:

```powershell
git lfs ls-files -- "apps/privyou/data/pms/dumps/dampsoft/production/practices/peschel/DS/daten/PATINFO.csv"
```

Pruefen, ob die Datei schon in Commits des Branches enthalten ist:

```powershell
git log --oneline -- "apps/privyou/data/pms/dumps/dampsoft/production/practices/peschel/DS/daten/PATINFO.csv"
```

## Richtige Interpretation
- Wenn `git check-attr` `filter: lfs` zeigt, ist die Regel aktuell korrekt.
- Wenn `git lfs ls-files` die Datei nicht zeigt, ist sie aktuell nicht als LFS-Datei gespeichert.
- Selbst bei korrekter Regel bleibt der Push blockiert, wenn in der Branch-Historie noch ein normaler grosser Blob enthalten ist.

## Sauberer Fix
Wenn die problematischen Commits noch nicht erfolgreich auf das Ziel-Remote gepusht wurden:

```powershell
git lfs install
git lfs migrate import --include="apps/privyou/data/pms/dumps/**,apps/privyou/data/pms/references/**"
```

Danach erneut pushen.

## Warum dieser Fix funktioniert
`git lfs migrate import` ersetzt die grossen normalen Git-Blobs in den betroffenen Commits durch Git-LFS-Pointer.

Das ist entscheidend, weil:
- ein einfaches erneutes `git add` nur den aktuellen Stand korrigieren kann
- GitHub aber die komplette eingehende Push-Historie prueft

## Verifikation nach dem Fix
Pruefen, ob die Datei jetzt von LFS verwaltet wird:

```powershell
git lfs ls-files -- "apps/privyou/data/pms/dumps/dampsoft/production/practices/peschel/DS/daten/PATINFO.csv"
```

Pruefen, ob im Commit nur noch ein kleiner Pointer gespeichert ist:

```powershell
git cat-file -s HEAD:"apps/privyou/data/pms/dumps/dampsoft/production/practices/peschel/DS/daten/PATINFO.csv"
```

Dann Push erneut ausfuehren:

```powershell
git push github-nelly HEAD
```

## Merksatz
Eine `.gitattributes`-Regel fuer Git LFS ist keine nachtraegliche Migration bestehender Commits.

## Best Practice
- LFS-Regeln immer vor dem ersten `git add` grosser Dateien anlegen.
- `git lfs install` auf der Maschine frueh ausfuehren.
- Nach LFS-Regelaenderungen betroffene Dateien nur dann als "sauber" betrachten, wenn sie wirklich als LFS-Dateien im Index bzw. in der Historie liegen.
- Bei Push-Fehlern nicht nur den aktuellen Dateistand pruefen, sondern die gesamte Branch-Historie beachten.
