# Referenzdokument — Git LFS Anchor-Thematik

## 1. Einordnung

Git Large File Storage (Git LFS) ist eine Erweiterung für Git, die große Dateien (z. B. Binärdateien, Modelle, Medien) nicht direkt im Repository speichert, sondern durch sogenannte **Pointer-Dateien (Anchors)** referenziert.

Architekturell trennt Git LFS:

* **Metadaten (Pointer / Anchor)** → im Git-Repository
* **Inhaltsdaten (Binary Blobs)** → im LFS-Storage (extern)

---

## 2. Entstehung und Sichtbarkeit eines Anchors

Ein **Anchor (Pointer-Datei)** entsteht, wenn eine Datei durch Git LFS verwaltet wird, z. B. via:

```bash
git lfs track "*.zip"
git add .gitattributes
git add file.zip
git commit -m "Track large files via LFS"
```

### Inhalt eines Anchors

Eine typische Pointer-Datei sieht z. B. so aus:

```text
version https://git-lfs.github.com/spec/v1
oid sha256:3b83ef96387f14655fc854ddc3c6bd57
size 123456789
```

### Sichtbarkeit im Projekt

Beim Klonen oder Arbeiten mit dem Repository kann es passieren, dass:

* nur der **Anchor-Text** sichtbar ist
* die eigentliche Datei **nicht lokal vorhanden** ist

Beispiel:

```bash
cat file.zip
```

→ Ausgabe ist **nicht die Binärdatei**, sondern der Pointer-Inhalt.

Das bedeutet architekturell:

* Git enthält **nur Referenzen**
* Die **Daten müssen separat bezogen werden**

---

## 3. Vollständiges Beziehen mit `git lfs pull`

Um alle referenzierten LFS-Dateien vollständig herunterzuladen, kann folgender Befehl verwendet werden:

```bash
git lfs pull
```

### Wirkung

* Lädt **alle benötigten LFS-Objekte** für den aktuellen Checkout
* Ersetzt Pointer-Dateien durch die tatsächlichen Inhalte im Working Directory

Optional mit explizitem Remote/Branch:

```bash
git lfs pull origin main
```

---

## 4. Einschränkung — Nicht immer das erwartete Verhalten

Das Verhalten von `git lfs pull` ist nicht in allen Szenarien optimal.

### Problemstellung

In bestimmten Architekturen oder Workflows:

* sind LFS-Dateien **sehr groß**
* werden **nicht alle Dateien benötigt**
* ist Bandbreite oder Speicher begrenzt

### Konsequenz

Ein globales:

```bash
git lfs pull
```

kann:

* unnötig viele Daten laden
* Build- oder CI-Prozesse verlangsamen
* lokale Ressourcen überlasten

---

## 5. Alternative — Sequentielles Vorgehen

Statt alle Dateien gleichzeitig zu laden, kann ein **selektiver bzw. sequentieller Ansatz** verwendet werden.

### 5.1 Selektives Fetching

```bash
git lfs fetch --include="path/to/needed/file"
```

### 5.2 Checkout einzelner Dateien

```bash
git lfs checkout path/to/needed/file
```

### 5.3 Kombination (empfohlen)

```bash
git lfs fetch --include="*.model"
git lfs checkout
```

### 5.4 Ausschluss bestimmter Dateien

```bash
git lfs fetch --exclude="*.zip"
```

---

## 6. Architekturell korrekte Betrachtung

### Schichtenmodell

1. **Git-Ebene**

   * Versioniert Pointer-Dateien
   * Kennt keine großen Binärdaten

2. **LFS-Transportebene**

   * Verantwortlich für Download/Upload
   * Kommuniziert mit LFS-Server

3. **Working Directory**

   * Enthält entweder:

     * Pointer (nicht geladen)
     * oder echte Dateien (nach Checkout)

### Datenfluss

```text
Git Repo (Pointer)
        ↓
 git lfs fetch
        ↓
 LFS Storage (Binary)
        ↓
 git lfs checkout
        ↓
 Working Directory (echte Datei)
```

### Wichtige Trennung

| Ebene     | Inhalt  | Verantwortung        |
| --------- | ------- | -------------------- |
| Git       | Pointer | Versionierung        |
| LFS       | Binary  | Speicherung/Transfer |
| Workspace | Datei   | Nutzung              |

---

## 7. Typische Best Practices

* Verwende `git lfs pull` nur, wenn **alle Dateien benötigt werden**
* Nutze selektives Fetching in:

  * CI/CD
  * großen Monorepos
  * datenintensiven Projekten
* Kontrolliere `.gitattributes` bewusst

---

## 8. Zusammenfassung

* Anchors (Pointer-Dateien) sind **zentrale Architekturkomponente** von Git LFS
* Sichtbarer Inhalt kann **nur Metadaten** sein
* `git lfs pull` lädt **alle Inhalte**, ist aber nicht immer optimal
* Sequentielle Strategien ermöglichen **effiziente und kontrollierte Nutzung**
