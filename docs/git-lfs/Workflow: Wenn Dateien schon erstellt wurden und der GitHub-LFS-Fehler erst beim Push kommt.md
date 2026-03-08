## Workflow: Wenn Dateien schon erstellt wurden und der GitHub-LFS-Fehler erst beim Push kommt

### Ausgangslage
Du hast große Dateien wie `papers.json` oder `*.sqlite` bereits erzeugt, committed und erst beim Push die Ablehnung von GitHub bekommen.

Der wichtige Punkt ist: `.gitattributes` allein löst das Problem dann noch nicht rückwirkend. Die großen Dateien liegen zu diesem Zeitpunkt bereits als normale Git-Blobs in deiner lokalen Historie.

## Reihenfolge

1. **Git LFS-Regeln festlegen**
   
   Zuerst definierst du in `.gitattributes`, welche Dateien künftig über Git LFS laufen sollen.
   
   Beispiel:
   ```gitattributes
   papers.json filter=lfs diff=lfs merge=lfs -text
   *.sqlite filter=lfs diff=lfs merge=lfs -text
   ```

2. **Git LFS initialisieren**
   
   Danach stellst du sicher, dass Git LFS lokal verfügbar und aktiviert ist.
   
   ```bash
   git lfs install
   ```

3. **Verstehen, warum der Push trotzdem noch fehlschlägt**
   
   Wenn der Fehler erst nach dem Commit oder Push-Versuch auftaucht, sind die Dateien schon als normale Objekte in der lokalen Commit-Historie enthalten.
   
   Das bedeutet:
   - `.gitattributes` gilt ab jetzt
   - die schon existierenden Commits müssen aber noch auf LFS umgeschrieben werden

4. **Lokale, noch nicht akzeptierte Historie auf LFS migrieren**
   
   Genau das macht der entscheidende Schritt:
   
   ```bash
   git lfs migrate import --include="papers.json,*.sqlite"
   ```
   
   Dadurch werden die betroffenen Dateien in den lokalen Commits durch LFS-Pointer ersetzt.

5. **Prüfen, ob die Migration funktioniert hat**
   
   Danach kontrollierst du, ob Git die Dateien jetzt wirklich als LFS-Dateien führt.
   
   ```bash
   git lfs ls-files
   ```

6. **Erneut pushen**
   
   Erst jetzt ist der Branch in einem Zustand, den GitHub akzeptieren kann.
   
   ```bash
   git push origin main
   ```

## Kurzfassung als Merksatz

Wenn der Fehler **erst nach dem Commit** auftritt, reicht `.gitattributes` **nicht allein** aus.  
Dann ist die richtige Reihenfolge:

```bash
git lfs install
# .gitattributes anlegen oder korrigieren
git lfs migrate import --include="papers.json,*.sqlite"
git lfs ls-files
git push origin main
```

## Warum das funktioniert

Der entscheidende Recovery-Schritt ist nicht das bloße Aktivieren von LFS, sondern das **Umschreiben der lokalen Historie**, damit die großen Dateien nicht mehr als normale Git-Blobs, sondern als **LFS-Pointer** im Commit-Graph stehen.

## Wichtiger Hinweis

Dieses Vorgehen ist sauber, solange die betroffenen Commits **noch nicht erfolgreich auf den Remote gelangt** sind.  
Wenn die Historie bereits geteilt wurde, muss man bei einem Rewrite deutlich vorsichtiger vorgehen.








## 1. Lokale Inhalte wieder materialisieren

Wenn die Dateien nach der LFS-Migration nur noch die 3-zeiligen Pointer zeigen, ist das meist ein normaler Checkout-Zustand. Die echten Inhalte kannst du zuerst **ohne Netzwerk** wieder in den Working Tree holen:

```bash
git lfs checkout "tooling/crawler/pages/arxiv/prompt/output/papers.json" "tooling/crawler/pages/arxiv/prompt/storage/papers.sqlite"
```

Wenn die Objekte lokal nicht mehr im LFS-Cache liegen, hole sie von dem Remote, bei dem LFS bereits funktioniert, und materialisiere sie danach erneut:

```bash
git lfs pull origin --include="tooling/crawler/pages/arxiv/prompt/output/papers.json,tooling/crawler/pages/arxiv/prompt/storage/papers.sqlite"
git lfs checkout "tooling/crawler/pages/arxiv/prompt/output/papers.json" "tooling/crawler/pages/arxiv/prompt/storage/papers.sqlite"
```

So prüfst du das Ergebnis schnell:

```bash
ls -lh "tooling/crawler/pages/arxiv/prompt/output/papers.json" "tooling/crawler/pages/arxiv/prompt/storage/papers.sqlite"
```

Erwartung:
- `papers.json` ist wieder eine große JSON-Datei.
- `papers.sqlite` ist wieder eine große Binärdatei und nicht mehr der kleine LFS-Pointertext.


