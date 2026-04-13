# Praktische Übungen – Implementierungsplan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 6 eigenständige AsciiDoc-Übungsdateien und die zugehörigen Beispieldokumente für den Windows 11 Refresher Workshop erstellen.

**Architecture:** Jede Übung ist eine selbständige `.adoc`-Datei unter `docs/`. Beispieldokumente (DOCX, PDF, Bilder) werden per Python-Skript generiert und als ZIP-Archive in `docs/beispieldateien/` abgelegt. Der Build läuft via `asciidoctor` lokal und im CI.

**Tech Stack:** AsciiDoc / Asciidoctor 2.0, Python 3 (python-docx, reportlab, requests), Bash, Git

---

## Dateiübersicht

| Aktion | Pfad | Zweck |
|--------|------|-------|
| Erstellen | `docs/uebung-modul-1.adoc` | Übung: Datei-Explorer |
| Erstellen | `docs/uebung-modul-2.adoc` | Übung: Zwischenablage & Screenshots |
| Erstellen | `docs/uebung-modul-3.adoc` | Übung: PDF & ZIP |
| Erstellen | `docs/uebung-modul-4.adoc` | Übung: Programme installieren |
| Erstellen | `docs/uebung-modul-5.adoc` | Übung: OneDrive |
| Erstellen | `docs/uebung-modul-6.adoc` | Übung: Dokumente teilen |
| Erstellen | `docs/beispieldateien/kurs-dateien-modul1.zip` | 6 Fotos + Packliste.docx |
| Erstellen | `docs/beispieldateien/kurs-dateien-modul3.zip` | Brief.docx + 2 PDF-Belege |
| Erstellen | `scripts/create_beispieldateien.py` | Generierungsskript |

---

## Task 1: Python-Abhängigkeiten installieren

**Files:**
- (keine Datei, nur Systeminstallation)

- [ ] **Schritt 1: python-docx und reportlab installieren**

```bash
pip3 install python-docx reportlab requests
```

Erwartete Ausgabe: `Successfully installed python-docx-... reportlab-... requests-...`

- [ ] **Schritt 2: Installation verifizieren**

```bash
python3 -c "import docx, reportlab, requests; print('alle Pakete OK')"
```

Erwartete Ausgabe: `alle Pakete OK`

---

## Task 2: Beispieldateien-Verzeichnis und Generierungsskript erstellen

**Files:**
- Erstellen: `docs/beispieldateien/.gitkeep`
- Erstellen: `scripts/create_beispieldateien.py`

- [ ] **Schritt 1: Verzeichnisse anlegen**

```bash
mkdir -p docs/beispieldateien scripts
touch docs/beispieldateien/.gitkeep
```

- [ ] **Schritt 2: Generierungsskript erstellen**

Datei `scripts/create_beispieldateien.py`:

```python
#!/usr/bin/env python3
"""
Generiert die Beispieldateien für den Windows 11 Refresher Workshop.
Ausgabe: docs/beispieldateien/kurs-dateien-modul1.zip
         docs/beispieldateien/kurs-dateien-modul3.zip
"""

import os
import zipfile
import requests
from docx import Document
from docx.shared import Pt
from reportlab.pdfgen import canvas
from reportlab.lib.pagesizes import A4

BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
OUT_DIR = os.path.join(BASE_DIR, "docs", "beispieldateien")
TMP_DIR = os.path.join(BASE_DIR, "tmp_build")
os.makedirs(OUT_DIR, exist_ok=True)
os.makedirs(TMP_DIR, exist_ok=True)


# ── Modul 1: Ferienfotos + Packliste ──────────────────────────────────────────

# Lizenzfreie CC0-Fotos von Unsplash (direkter Download via Unsplash Source)
FOTOS = [
    ("Strand_Sonnenuntergang.jpg",  "https://images.unsplash.com/photo-1507525428034-b723cf961d3e?w=800&q=60"),
    ("Strand_Wellen.jpg",           "https://images.unsplash.com/photo-1519046904884-53103b34b206?w=800&q=60"),
    ("Berge_Panorama.jpg",          "https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=800&q=60"),
    ("Berge_Wanderweg.jpg",         "https://images.unsplash.com/photo-1501854140801-50d01698950b?w=800&q=60"),
    ("Altstadt_Gasse.jpg",          "https://images.unsplash.com/photo-1467269204594-9661b134dd2b?w=800&q=60"),
    ("Markt_Fruechte.jpg",          "https://images.unsplash.com/photo-1488459716781-31db52582fe9?w=800&q=60"),
]

def download_fotos():
    paths = []
    for filename, url in FOTOS:
        dest = os.path.join(TMP_DIR, filename)
        if not os.path.exists(dest):
            print(f"  Lade {filename} herunter …")
            r = requests.get(url, timeout=30)
            r.raise_for_status()
            with open(dest, "wb") as f:
                f.write(r.content)
        paths.append(dest)
    return paths


def create_packliste_docx():
    doc = Document()
    doc.add_heading("Packliste – Familienausflug", level=1)
    doc.add_paragraph("Erstellt von: (Ihr Name)", style="Normal")
    doc.add_paragraph("")
    doc.add_heading("Kleidung", level=2)
    for item in ["T-Shirts (3x)", "Pullover", "Regenjacke", "Bequeme Schuhe"]:
        p = doc.add_paragraph(item, style="List Bullet")
    doc.add_heading("Verpflegung", level=2)
    for item in ["Wasserflasche", "Sandwiches", "Obst", "Snacks"]:
        p = doc.add_paragraph(item, style="List Bullet")
    doc.add_heading("Sonstiges", level=2)
    for item in ["Sonnencreme", "Erste-Hilfe-Set", "Kamera", "Wanderkarte"]:
        p = doc.add_paragraph(item, style="List Bullet")
    path = os.path.join(TMP_DIR, "Packliste-Familie.docx")
    doc.save(path)
    print("  Packliste-Familie.docx erstellt")
    return path


def create_modul1_zip(foto_paths, packliste_path):
    zip_path = os.path.join(OUT_DIR, "kurs-dateien-modul1.zip")
    with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as zf:
        for p in foto_paths:
            zf.write(p, os.path.basename(p))
        zf.write(packliste_path, "Packliste-Familie.docx")
    print(f"  → {zip_path}")
    return zip_path


# ── Modul 3: Brief + PDF-Belege ───────────────────────────────────────────────

def create_brief_docx():
    doc = Document()
    doc.add_heading("Brief an den Treuhänder", level=1)
    doc.add_paragraph("")
    doc.add_paragraph("Muster, 13. April 2026", style="Normal")
    doc.add_paragraph("")
    doc.add_paragraph("Sehr geehrte Damen und Herren,", style="Normal")
    doc.add_paragraph("")
    doc.add_paragraph(
        "anbei sende ich Ihnen die gewünschten Unterlagen für die "
        "Steuererklärung 2025. Die Belege finden Sie als separate Dateien.",
        style="Normal"
    )
    doc.add_paragraph("")
    doc.add_paragraph("Mit freundlichen Grüssen", style="Normal")
    doc.add_paragraph("")
    doc.add_paragraph("(Ihr Name)", style="Normal")
    path = os.path.join(TMP_DIR, "Brief-an-Treuhänder.docx")
    doc.save(path)
    print("  Brief-an-Treuhänder.docx erstellt")
    return path


def create_beleg_pdf(filename, titel, betrag, datum, absender):
    path = os.path.join(TMP_DIR, filename)
    c = canvas.Canvas(path, pagesize=A4)
    width, height = A4
    c.setFont("Helvetica-Bold", 16)
    c.drawString(60, height - 80, titel)
    c.setFont("Helvetica", 11)
    c.drawString(60, height - 120, f"Datum: {datum}")
    c.drawString(60, height - 145, f"Absender: {absender}")
    c.drawString(60, height - 170, f"Betrag: CHF {betrag}")
    c.setFont("Helvetica", 9)
    c.drawString(60, 40, "Beispieldokument – Windows 11 Refresher Workshop – Pro Senectute")
    c.save()
    print(f"  {filename} erstellt")
    return path


def create_modul3_zip(brief_path, beleg1_path, beleg2_path):
    zip_path = os.path.join(OUT_DIR, "kurs-dateien-modul3.zip")
    with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as zf:
        zf.write(brief_path,  "Brief-an-Treuhänder.docx")
        zf.write(beleg1_path, "Beleg-Krankenkasse.pdf")
        zf.write(beleg2_path, "Beleg-Strom.pdf")
    print(f"  → {zip_path}")
    return zip_path


# ── Hauptprogramm ─────────────────────────────────────────────────────────────

if __name__ == "__main__":
    print("=== Modul 1 ===")
    fotos   = download_fotos()
    pkliste = create_packliste_docx()
    create_modul1_zip(fotos, pkliste)

    print("=== Modul 3 ===")
    brief  = create_brief_docx()
    beleg1 = create_beleg_pdf(
        "Beleg-Krankenkasse.pdf",
        "Krankenkasse Musterland – Jahresabrechnung 2025",
        "1'840.00", "31.12.2025", "Krankenkasse Musterland AG, 6000 Luzern"
    )
    beleg2 = create_beleg_pdf(
        "Beleg-Strom.pdf",
        "Stadtwerke Musterstadt – Stromrechnung 2025",
        "312.50", "15.01.2026", "Stadtwerke Musterstadt, 6001 Luzern"
    )
    create_modul3_zip(brief, beleg1, beleg2)

    print("\nFertig. Dateien in docs/beispieldateien/")
```

- [ ] **Schritt 3: Skript ausführen**

```bash
cd /pfad/zum/repo
python3 scripts/create_beispieldateien.py
```

Erwartete Ausgabe:
```
=== Modul 1 ===
  Lade Strand_Sonnenuntergang.jpg herunter …
  ...
  → docs/beispieldateien/kurs-dateien-modul1.zip
=== Modul 3 ===
  Brief-an-Treuhänder.docx erstellt
  ...
  → docs/beispieldateien/kurs-dateien-modul3.zip

Fertig. Dateien in docs/beispieldateien/
```

- [ ] **Schritt 4: ZIP-Inhalte prüfen**

```bash
python3 -c "
import zipfile
with zipfile.ZipFile('docs/beispieldateien/kurs-dateien-modul1.zip') as z:
    print('Modul 1:', z.namelist())
with zipfile.ZipFile('docs/beispieldateien/kurs-dateien-modul3.zip') as z:
    print('Modul 3:', z.namelist())
"
```

Erwartete Ausgabe:
```
Modul 1: ['Strand_Sonnenuntergang.jpg', 'Strand_Wellen.jpg', 'Berge_Panorama.jpg', 'Berge_Wanderweg.jpg', 'Altstadt_Gasse.jpg', 'Markt_Fruechte.jpg', 'Packliste-Familie.docx']
Modul 3: ['Brief-an-Treuhänder.docx', 'Beleg-Krankenkasse.pdf', 'Beleg-Strom.pdf']
```

- [ ] **Schritt 5: Committen**

```bash
git add docs/beispieldateien/ scripts/create_beispieldateien.py
git commit -m "feat: add example files generator and generated ZIP archives"
```

---

## Task 3: Übungsdatei Modul 1 – Datei-Explorer

**Files:**
- Erstellen: `docs/uebung-modul-1.adoc`

- [ ] **Schritt 1: Datei erstellen**

Datei `docs/uebung-modul-1.adoc`:

```adoc
= Übung Modul 1: Datei-Explorer und Ordnerstrukturen
:lang: de
:icons: font
:experimental:

== Szenario

Ihre Enkelin Clara war in den Sommerferien unterwegs und hat Ihnen sechs Fotos geschickt.
Alle Bilder sind bunt gemischt in Ihrem Download-Ordner gelandet.
In dieser Übung legen Sie eine übersichtliche Ordnerstruktur an und sortieren die Fotos so, dass Sie sie später leicht wiederfinden.

== Vorbereitung

Laden Sie die Übungsdateien von der Kurswebseite herunter:

link:beispieldateien/kurs-dateien-modul1.zip[kurs-dateien-modul1.zip herunterladen]

== Aufgaben

=== Aufgabe 1: ZIP-Datei entpacken

. Öffnen Sie den Datei-Explorer mit kbd:[Win+E].
. Navigieren Sie zu Ihrem *Downloads*-Ordner.
. Machen Sie einen Doppelklick auf `kurs-dateien-modul1.zip`.
. Klicken Sie auf *«Alle extrahieren»* und bestätigen Sie.

[TIP]
====
Wenn Sie «Alle extrahieren» nicht sehen: Rechtsklick auf die ZIP-Datei → *«Alle extrahieren»*.
====

=== Aufgabe 2: Ordnerstruktur anlegen

Erstellen Sie im Ordner *Dokumente* folgende Struktur:

....
Dokumente/
└── Claras Ferien 2026/
    ├── Strand/
    ├── Berge/
    └── Sonstiges/
....

. Öffnen Sie *Dokumente* im Datei-Explorer.
. Rechtsklick in einen leeren Bereich → *«Neu»* → *«Ordner»*.
. Tippen Sie `Claras Ferien 2026` und drücken Sie kbd:[Enter].
. Öffnen Sie den neuen Ordner per Doppelklick.
. Erstellen Sie darin drei Unterordner: `Strand`, `Berge`, `Sonstiges`.

[TIP]
====
Mit kbd:[Ctrl+Shift+N] legen Sie direkt einen neuen Ordner an, ohne Rechtsklick.
====

=== Aufgabe 3: Fotos sortieren

Verschieben Sie die 6 Fotos aus dem entpackten Ordner in die passenden Unterordner:

* `Strand_Sonnenuntergang.jpg` → Ordner *Strand*
* `Strand_Wellen.jpg` → Ordner *Strand*
* `Berge_Panorama.jpg` → Ordner *Berge*
* `Berge_Wanderweg.jpg` → Ordner *Berge*
* `Altstadt_Gasse.jpg` → Ordner *Sonstiges*
* `Markt_Fruechte.jpg` → Ordner *Sonstiges*

Ziehen Sie jedes Foto per Drag-and-Drop in den richtigen Ordner.

[TIP]
====
Öffnen Sie zwei Datei-Explorer-Fenster nebeneinander: Erstes Fenster mit den Fotos, zweites Fenster mit der Zielstruktur.
Zweites Fenster öffnen: kbd:[Win+E] nochmals drücken.
====

=== Aufgabe 4: Datei umbenennen

Das Foto `Markt_Fruechte.jpg` soll einen persönlicheren Namen bekommen.

. Klicken Sie einmal auf `Markt_Fruechte.jpg`, um es auszuwählen.
. Drücken Sie kbd:[F2].
. Tippen Sie den neuen Namen: `Markt_Luzern.jpg`
. Drücken Sie kbd:[Enter].

[TIP]
====
Alternativ: Rechtsklick → *«Umbenennen»*.
====

== Erfolgskontrolle

Das haben Sie geschafft, wenn:

* [x] Im Ordner *Claras Ferien 2026* drei Unterordner vorhanden sind (`Strand`, `Berge`, `Sonstiges`)
* [x] Jeder Unterordner genau die richtigen Fotos enthält
* [x] Das Foto im Sonstiges-Ordner den neuen Namen `Markt_Luzern.jpg` trägt
* [x] Die Datei `Packliste-Familie.docx` noch im entpackten Ordner liegt (wird in Modul 6 verwendet)
```

- [ ] **Schritt 2: Build prüfen**

```bash
asciidoctor -D build docs/uebung-modul-1.adoc && echo "BUILD OK"
```

Erwartete Ausgabe: `BUILD OK` (keine Fehler oder Warnungen)

- [ ] **Schritt 3: Committen**

```bash
git add docs/uebung-modul-1.adoc
git commit -m "feat: add Uebung Modul 1 – Datei-Explorer"
```

---

## Task 4: Übungsdatei Modul 2 – Zwischenablage & Screenshots

**Files:**
- Erstellen: `docs/uebung-modul-2.adoc`

- [ ] **Schritt 1: Datei erstellen**

Datei `docs/uebung-modul-2.adoc`:

```adoc
= Übung Modul 2: Zwischenablage und Screenshots
:lang: de
:icons: font
:experimental:

== Szenario

Beim Stöbern im Internet haben Sie ein wunderbares Rezept für einen Zitronenkuchen gefunden.
Sie möchten den Rezepttext für sich speichern und einem Freund mitschicken.
Dazu nutzen Sie die Zwischenablage und das Snipping Tool.

== Vorbereitung

Kein Download nötig. Sie brauchen:

* Einen Webbrowser (z. B. Microsoft Edge)
* Das Programm *Editor* (Notepad)

Öffnen Sie diese Rezeptseite im Browser:
`https://www.chefkoch.de` (beliebiges Rezept wählen)

== Aufgaben

=== Aufgabe 1: Rezeptname kopieren und einfügen

. Öffnen Sie *Editor* (Notepad): kbd:[Win] drücken, `Editor` eintippen, Enter.
. Wechseln Sie in den Browser und markieren Sie den *Titel des Rezepts*.
. Drücken Sie kbd:[Ctrl+C] (Kopieren).
. Wechseln Sie zurück in den Editor.
. Drücken Sie kbd:[Ctrl+V] (Einfügen).

[TIP]
====
Zwischen Fenstern wechseln: kbd:[Alt+Tab] — halten Sie kbd:[Alt] gedrückt und drücken Sie kbd:[Tab], um das gewünschte Fenster auszuwählen.
====

=== Aufgabe 2: Zutaten kopieren

. Markieren Sie im Browser die *Zutatenliste* des Rezepts.
. Drücken Sie kbd:[Ctrl+C].
. Wechseln Sie in den Editor und klicken Sie ans Ende des Textes.
. Drücken Sie kbd:[Enter] für eine neue Zeile, dann kbd:[Ctrl+V].

=== Aufgabe 3: Zwischenablageverlauf nutzen

. Drücken Sie kbd:[Win+V], um den Zwischenablageverlauf zu öffnen.
. Falls er noch nicht aktiviert ist: Klicken Sie auf *«Aktivieren»*.
. Kopieren Sie im Browser noch einen weiteren Textabschnitt (z. B. die Zubereitungsschritte).
. Öffnen Sie erneut kbd:[Win+V] — Sie sehen jetzt mehrere Einträge.
. Klicken Sie auf den ersten Eintrag (Rezepttitel), um ihn nochmals einzufügen.

[TIP]
====
Der Zwischenablageverlauf speichert bis zu 25 kopierte Elemente.
Sie können damit jederzeit auf ältere Kopien zurückgreifen.
====

=== Aufgabe 4: Screenshot erstellen

. Wechseln Sie in den Browser mit dem Rezept.
. Drücken Sie kbd:[Win+Shift+S].
. Ziehen Sie mit der Maus einen Rahmen um das Rezeptbild und den Titel.
. Der Screenshot wird in der Zwischenablage gespeichert.

[TIP]
====
Nach dem Screenshot erscheint unten rechts eine Benachrichtigung.
Klicken Sie darauf, um das Bild im Snipping Tool zu öffnen und zu speichern.
====

=== Aufgabe 5: Screenshot in den Editor einfügen

. Wechseln Sie in den Editor.
. Klicken Sie ans Ende des Textes.
. Drücken Sie kbd:[Ctrl+V].

NOTE: Im einfachen Editor (Notepad) können keine Bilder eingefügt werden.
Öffnen Sie stattdessen *WordPad* (kbd:[Win] → `WordPad` suchen) oder *Word*.

. Speichern Sie das Dokument als `Kuchenrezept.rtf`.

== Erfolgskontrolle

Das haben Sie geschafft, wenn:

* [x] Rezeptname und Zutaten im Editor stehen
* [x] Der Zwischenablageverlauf (kbd:[Win+V]) mindestens 2 Einträge zeigt
* [x] Ein Screenshot des Rezepts erstellt wurde
* [x] Das Dokument `Kuchenrezept.rtf` gespeichert ist
```

- [ ] **Schritt 2: Build prüfen**

```bash
asciidoctor -D build docs/uebung-modul-2.adoc && echo "BUILD OK"
```

- [ ] **Schritt 3: Committen**

```bash
git add docs/uebung-modul-2.adoc
git commit -m "feat: add Uebung Modul 2 – Zwischenablage und Screenshots"
```

---

## Task 5: Übungsdatei Modul 3 – PDF & ZIP

**Files:**
- Erstellen: `docs/uebung-modul-3.adoc`

- [ ] **Schritt 1: Datei erstellen**

Datei `docs/uebung-modul-3.adoc`:

```adoc
= Übung Modul 3: PDF- und ZIP-Dateien
:lang: de
:icons: font
:experimental:

== Szenario

Ihr Treuhänder benötigt Unterlagen für Ihre Steuererklärung.
Er hat Sie gebeten, einen kurzen Brief sowie zwei Belege (Krankenkasse und Strom) als eine einzige ZIP-Datei per E-Mail zu schicken.
In dieser Übung erstellen Sie den Brief als PDF und packen alles in ein ZIP-Archiv.

== Vorbereitung

Laden Sie die Übungsdateien von der Kurswebseite herunter:

link:beispieldateien/kurs-dateien-modul3.zip[kurs-dateien-modul3.zip herunterladen]

== Aufgaben

=== Aufgabe 1: ZIP-Datei entpacken

. Öffnen Sie den Datei-Explorer mit kbd:[Win+E].
. Navigieren Sie in den *Downloads*-Ordner.
. Doppelklick auf `kurs-dateien-modul3.zip`.
. Klicken Sie auf *«Alle extrahieren»* → Zielordner bestätigen → *«Extrahieren»*.

[TIP]
====
Merken Sie sich den Zielordner, damit Sie die Dateien später leicht wiederfinden.
====

=== Aufgabe 2: Brief in Word öffnen und personalisieren

. Doppelklick auf `Brief-an-Treuhänder.docx`.
. Ersetzen Sie `(Ihr Name)` durch Ihren eigenen Namen.
. Speichern Sie das Dokument: kbd:[Ctrl+S].

=== Aufgabe 3: Brief als PDF speichern

. Klicken Sie in Word auf *«Datei»* → *«Drucken»*.
. Wählen Sie als Drucker: *«Microsoft Print to PDF»*.
. Klicken Sie auf *«Drucken»*.
. Speichern Sie die PDF-Datei als `Brief-Treuhänder.pdf` im selben Ordner wie die anderen Belege.

[TIP]
====
Falls *Microsoft Print to PDF* nicht erscheint: Klicken Sie auf den Drucker-Namen, um eine Liste aller verfügbaren Drucker zu sehen.
====

=== Aufgabe 4: Ordner für Unterlagen anlegen

. Erstellen Sie in *Dokumente* einen neuen Ordner: `Unterlagen-Treuhänder-2026`.
. Verschieben Sie folgende Dateien in diesen Ordner:
** `Brief-Treuhänder.pdf`
** `Beleg-Krankenkasse.pdf`
** `Beleg-Strom.pdf`

=== Aufgabe 5: ZIP-Datei erstellen

. Öffnen Sie den Ordner `Unterlagen-Treuhänder-2026`.
. Markieren Sie alle drei PDF-Dateien (kbd:[Ctrl+A]).
. Rechtsklick → *«Komprimieren zu ZIP-Datei»*.
. Benennen Sie die ZIP-Datei: `Unterlagen-Steuern-2025.zip`.

[TIP]
====
Falls *«Komprimieren zu ZIP-Datei»* nicht erscheint, suchen Sie nach *«Senden an»* → *«ZIP-komprimierter Ordner»*.
====

=== Aufgabe 6: ZIP-Inhalt prüfen

. Doppelklick auf `Unterlagen-Steuern-2025.zip`.
. Prüfen Sie, ob alle drei PDF-Dateien darin enthalten sind.

== Erfolgskontrolle

Das haben Sie geschafft, wenn:

* [x] Der Brief als `Brief-Treuhänder.pdf` gespeichert ist
* [x] Der Ordner `Unterlagen-Treuhänder-2026` die drei PDF-Dateien enthält
* [x] Die Datei `Unterlagen-Steuern-2025.zip` alle drei PDFs beinhaltet
* [x] Die ZIP-Datei bereit ist, per E-Mail verschickt zu werden
```

- [ ] **Schritt 2: Build prüfen**

```bash
asciidoctor -D build docs/uebung-modul-3.adoc && echo "BUILD OK"
```

- [ ] **Schritt 3: Committen**

```bash
git add docs/uebung-modul-3.adoc
git commit -m "feat: add Uebung Modul 3 – PDF und ZIP"
```

---

## Task 6: Übungsdatei Modul 4 – Programme installieren

**Files:**
- Erstellen: `docs/uebung-modul-4.adoc`

- [ ] **Schritt 1: Datei erstellen**

Datei `docs/uebung-modul-4.adoc`:

```adoc
= Übung Modul 4: Programme installieren
:lang: de
:icons: font
:experimental:

== Szenario

Sie möchten am Computer Radio hören – zum Beispiel während Sie Dokumente bearbeiten.
Im Microsoft Store gibt es dafür kostenlose Apps.
In dieser Übung suchen Sie eine Radio-App, installieren sie, probieren sie kurz aus und deinstallieren sie anschliessend wieder.

== Vorbereitung

Kein Download nötig. Sie brauchen:

* Eine aktive Internetverbindung
* Ihr Microsoft-Konto (für den Store-Download)

== Aufgaben

=== Aufgabe 1: Microsoft Store öffnen

. Drücken Sie kbd:[Win], tippen Sie `Microsoft Store` und drücken Sie kbd:[Enter].
. Der Store öffnet sich.

[TIP]
====
Alternativ: Klicken Sie auf das Store-Symbol in der Taskleiste (Einkaufstasche-Symbol).
====

=== Aufgabe 2: Radio-App suchen

. Klicken Sie oben in das Suchfeld des Stores.
. Tippen Sie `Radio` und drücken Sie kbd:[Enter].
. Schauen Sie sich die Ergebnisse an.

Wählen Sie eine der folgenden kostenlosen Apps:

* *Simple Radio* – einfache Bedienung, viele Sender
* *myTuner Radio* – grosse Senderauswahl

=== Aufgabe 3: App installieren

. Klicken Sie auf die gewählte App.
. Lesen Sie kurz die Beschreibung und Bewertungen.
. Klicken Sie auf *«Herunterladen»* oder *«Installieren»*.
. Warten Sie, bis die Installation abgeschlossen ist.

[TIP]
====
Falls Sie nach Ihrem Microsoft-Konto gefragt werden: Geben Sie die E-Mail-Adresse und das Passwort ein, mit dem Sie Windows nutzen.
====

=== Aufgabe 4: App öffnen und ausprobieren

. Klicken Sie nach der Installation auf *«Öffnen»*.
. Suchen Sie einen Schweizer Sender (z. B. *SRF 1* oder *Radio Pilatus*).
. Hören Sie einen Moment zu.

=== Aufgabe 5: App deinstallieren

. Drücken Sie kbd:[Win] und öffnen Sie *Einstellungen* (Zahnrad-Symbol).
. Gehen Sie zu *«Apps»* → *«Installierte Apps»*.
. Suchen Sie in der Liste nach der Radio-App.
. Klicken Sie auf die drei Punkte (⋯) rechts neben der App.
. Wählen Sie *«Deinstallieren»* und bestätigen Sie.

[TIP]
====
Alternativ: Rechtsklick auf die App im Startmenü → *«Deinstallieren»*.
====

== Erfolgskontrolle

Das haben Sie geschafft, wenn:

* [x] Sie eine Radio-App aus dem Microsoft Store installiert haben
* [x] Sie erfolgreich einen Radiosender gehört haben
* [x] Die App anschliessend vollständig deinstalliert wurde
* [x] Die App nicht mehr in der Liste der installierten Apps erscheint
```

- [ ] **Schritt 2: Build prüfen**

```bash
asciidoctor -D build docs/uebung-modul-4.adoc && echo "BUILD OK"
```

- [ ] **Schritt 3: Committen**

```bash
git add docs/uebung-modul-4.adoc
git commit -m "feat: add Uebung Modul 4 – Programme installieren"
```

---

## Task 7: Übungsdatei Modul 5 – OneDrive

**Files:**
- Erstellen: `docs/uebung-modul-5.adoc`

- [ ] **Schritt 1: Datei erstellen**

Datei `docs/uebung-modul-5.adoc`:

```adoc
= Übung Modul 5: OneDrive optimal einsetzen
:lang: de
:icons: font
:experimental:

== Szenario

In Modul 3 haben Sie wichtige Unterlagen für Ihren Treuhänder vorbereitet.
Nun wollen Sie sicherstellen, dass diese Dokumente nicht verloren gehen – auch wenn der Computer einmal ausfällt.
Sie speichern die Dateien in OneDrive, damit sie automatisch gesichert und von überall zugänglich sind.

== Vorbereitung

Sie benötigen:

* Die ZIP-Datei `Unterlagen-Steuern-2025.zip` aus Modul 3
* Ein Microsoft-Konto mit aktivem OneDrive

== Aufgaben

=== Aufgabe 1: OneDrive-Status prüfen

. Schauen Sie in die *Taskleiste* (unten rechts, neben der Uhr).
. Suchen Sie das OneDrive-Symbol: eine weisse Wolke ☁.
. Klicken Sie darauf und prüfen Sie den Status.

[TIP]
====
* Weisse Wolke = alles synchronisiert ✓
* Blaue Wolke = Synchronisation läuft
* Rotes X = Fehler, bitte anklicken und Anweisung folgen
====

=== Aufgabe 2: OneDrive im Datei-Explorer öffnen

. Öffnen Sie den Datei-Explorer mit kbd:[Win+E].
. Klicken Sie links in der Seitenleiste auf *«OneDrive»*.
. Sie sehen Ihre OneDrive-Ordner (Dokumente, Bilder usw.).

=== Aufgabe 3: Neuen Ordner in OneDrive anlegen

. Klicken Sie in OneDrive auf den Ordner *«Dokumente»*.
. Rechtsklick in einen leeren Bereich → *«Neu»* → *«Ordner»*.
. Benennen Sie den Ordner: `Treuhänder 2026`.

=== Aufgabe 4: Datei in OneDrive kopieren

. Navigieren Sie zur Datei `Unterlagen-Steuern-2025.zip` (in Dokumente → Unterlagen-Treuhänder-2026).
. Rechtsklick → *«Kopieren»*.
. Navigieren Sie zurück zu OneDrive → Dokumente → `Treuhänder 2026`.
. Rechtsklick → *«Einfügen»*.

[TIP]
====
Alternativ: Öffnen Sie zwei Datei-Explorer-Fenster und ziehen Sie die Datei per Drag-and-Drop in den OneDrive-Ordner.
====

=== Aufgabe 5: Synchronisation abwarten

. Schauen Sie das OneDrive-Symbol in der Taskleiste an.
. Warten Sie, bis die Wolke wieder weiss ist (Synchronisation abgeschlossen).
. Die Datei im Ordner zeigt ein grünes Häkchen ✓.

=== Aufgabe 6: Datei im Browser kontrollieren

. Öffnen Sie den Browser und gehen Sie zu: `https://onedrive.live.com`
. Melden Sie sich mit Ihrem Microsoft-Konto an (falls nötig).
. Navigieren Sie zu *Dokumente* → *Treuhänder 2026*.
. Prüfen Sie, ob `Unterlagen-Steuern-2025.zip` dort sichtbar ist.

[TIP]
====
Wenn die Datei noch nicht erscheint: Warten Sie eine Minute und laden Sie die Seite neu (kbd:[F5]).
====

== Erfolgskontrolle

Das haben Sie geschafft, wenn:

* [x] Der Ordner `Treuhänder 2026` in OneDrive existiert
* [x] Die Datei `Unterlagen-Steuern-2025.zip` im OneDrive-Ordner liegt
* [x] Das OneDrive-Symbol in der Taskleiste ein grünes Häkchen zeigt
* [x] Die Datei im Browser unter onedrive.live.com sichtbar ist
```

- [ ] **Schritt 2: Build prüfen**

```bash
asciidoctor -D build docs/uebung-modul-5.adoc && echo "BUILD OK"
```

- [ ] **Schritt 3: Committen**

```bash
git add docs/uebung-modul-5.adoc
git commit -m "feat: add Uebung Modul 5 – OneDrive"
```

---

## Task 8: Übungsdatei Modul 6 – Dokumente teilen

**Files:**
- Erstellen: `docs/uebung-modul-6.adoc`

- [ ] **Schritt 1: Datei erstellen**

Datei `docs/uebung-modul-6.adoc`:

```adoc
= Übung Modul 6: Dokumente teilen und gemeinsam bearbeiten
:lang: de
:icons: font
:experimental:

== Szenario

Sie planen mit Ihrer Tochter einen Familienausflug.
In Modul 1 haben Sie bereits die Datei `Packliste-Familie.docx` erhalten.
Jetzt möchten Sie diese Packliste mit Ihrer Tochter teilen, damit sie ebenfalls Dinge ergänzen kann – und das ganz ohne E-Mail-Anhang.

== Vorbereitung

Sie benötigen:

* Die Datei `Packliste-Familie.docx` aus Modul 1 (im Ordner `Claras Ferien 2026` oder Downloads)
* OneDrive (eingerichtet aus Modul 5)
* Ein Microsoft-Konto

== Aufgaben

=== Aufgabe 1: Packliste in OneDrive speichern

. Öffnen Sie den Datei-Explorer mit kbd:[Win+E].
. Navigieren Sie zur Datei `Packliste-Familie.docx`.
. Kopieren Sie die Datei (Rechtsklick → *«Kopieren»*).
. Navigieren Sie zu OneDrive → *Dokumente*.
. Fügen Sie die Datei ein (Rechtsklick → *«Einfügen»*).
. Warten Sie, bis das grüne Häkchen erscheint (Datei synchronisiert).

[TIP]
====
Falls die Datei bereits in OneDrive liegt (z. B. im Ordner Dokumente), können Sie diesen Schritt überspringen.
====

=== Aufgabe 2: Dokument freigeben

. Rechtsklick auf `Packliste-Familie.docx` im OneDrive-Ordner.
. Wählen Sie *«Teilen»* (oder *«OneDrive-Freigabelink kopieren»*).
. Es öffnet sich ein Fenster mit Freigabe-Einstellungen.

=== Aufgabe 3: Freigabe-Einstellungen prüfen

. Stellen Sie sicher, dass die Option *«Kann bearbeiten»* ausgewählt ist.
. Klicken Sie auf *«Link kopieren»*.

[TIP]
====
* *«Kann bearbeiten»* – Ihre Tochter kann Dinge hinzufügen und ändern.
* *«Kann anzeigen»* – Ihre Tochter sieht das Dokument nur, kann aber nichts ändern.
====

=== Aufgabe 4: Link per E-Mail versenden (Übungsmodus)

. Öffnen Sie Ihr E-Mail-Programm (z. B. Outlook oder Gmail im Browser).
. Erstellen Sie eine neue E-Mail an sich selbst.
. Fügen Sie den kopierten Link in den E-Mail-Text ein (kbd:[Ctrl+V]).
. Schreiben Sie eine kurze Nachricht: _«Hier ist die Packliste für unseren Ausflug – bitte ergänze deine Wünsche!»_
. Senden Sie die E-Mail.

=== Aufgabe 5: Dokument in Word Online öffnen und bearbeiten

. Öffnen Sie Ihre E-Mail und klicken Sie auf den Link.
. Das Dokument öffnet sich im Browser in *Word Online*.
. Fügen Sie am Ende der Packliste einen neuen Punkt hinzu (z. B. `Sonnenschutz LSF 50`).
. Word Online speichert automatisch – kein manuelles Speichern nötig.

[TIP]
====
Wenn Word Online fragt, ob Sie sich anmelden möchten: Melden Sie sich mit Ihrem Microsoft-Konto an, damit Ihre Änderungen gespeichert werden.
====

=== Aufgabe 6: Änderungen im Datei-Explorer prüfen

. Wechseln Sie in den Datei-Explorer zu OneDrive → Dokumente.
. Warten Sie einen Moment (Synchronisation).
. Öffnen Sie `Packliste-Familie.docx` – Ihr neuer Eintrag sollte sichtbar sein.

== Erfolgskontrolle

Das haben Sie geschafft, wenn:

* [x] `Packliste-Familie.docx` in OneDrive gespeichert und synchronisiert ist
* [x] Ein Freigabelink erstellt und per E-Mail versendet wurde
* [x] Das Dokument in Word Online geöffnet und bearbeitet wurde
* [x] Die Änderung auch im Datei-Explorer sichtbar ist
```

- [ ] **Schritt 2: Build prüfen**

```bash
asciidoctor -D build docs/uebung-modul-6.adoc && echo "BUILD OK"
```

- [ ] **Schritt 3: Committen**

```bash
git add docs/uebung-modul-6.adoc
git commit -m "feat: add Uebung Modul 6 – Dokumente teilen und gemeinsam bearbeiten"
```

---

## Task 9: Alle Dateien gemeinsam bauen und abschliessen

**Files:**
- Modifizieren: `docs/kursprogramm.adoc` (Download-Links hinzufügen)

- [ ] **Schritt 1: Alle 6 Übungsdateien auf einmal bauen**

```bash
mkdir -p build
asciidoctor -D build docs/uebung-modul-*.adoc && echo "ALLE BUILDS OK"
```

Erwartete Ausgabe: `ALLE BUILDS OK` (keine Fehler)

- [ ] **Schritt 2: Download-Hinweise in kursprogramm.adoc ergänzen**

Im bestehenden `docs/kursprogramm.adoc` bei Modul 1 (nach dem Abschnitt `==== Praxis: Datei-Explorer erkunden`) folgenden Block einfügen:

```adoc
[NOTE]
====
*Übungsblatt:* link:uebung-modul-1.html[Übung Modul 1 öffnen] +
*Download:* link:beispieldateien/kurs-dateien-modul1.zip[kurs-dateien-modul1.zip]
====
```

Bei Modul 2 (nach `==== Praxis: Zwischenablage und Screenshots`):

```adoc
[NOTE]
====
*Übungsblatt:* link:uebung-modul-2.html[Übung Modul 2 öffnen]
====
```

Bei Modul 3 (nach `==== Praxis: PDF und ZIP`):

```adoc
[NOTE]
====
*Übungsblatt:* link:uebung-modul-3.html[Übung Modul 3 öffnen] +
*Download:* link:beispieldateien/kurs-dateien-modul3.zip[kurs-dateien-modul3.zip]
====
```

Bei Modul 4 (nach `==== Praxis: Programme installieren und deinstallieren`):

```adoc
[NOTE]
====
*Übungsblatt:* link:uebung-modul-4.html[Übung Modul 4 öffnen]
====
```

Bei Modul 5 (nach `==== Praxis: OneDrive einrichten und nutzen`):

```adoc
[NOTE]
====
*Übungsblatt:* link:uebung-modul-5.html[Übung Modul 5 öffnen]
====
```

Bei Modul 6 (nach `==== Praxis: Teilen und zusammenarbeiten`):

```adoc
[NOTE]
====
*Übungsblatt:* link:uebung-modul-6.html[Übung Modul 6 öffnen]
====
```

- [ ] **Schritt 3: Kursprogramm bauen und prüfen**

```bash
asciidoctor -D build docs/kursprogramm.adoc && echo "BUILD OK"
```

- [ ] **Schritt 4: Abschluss-Commit**

```bash
git add docs/kursprogramm.adoc
git commit -m "feat: link exercise sheets from Kursprogramm"
```

---

## Spec-Abgleich

| Spec-Anforderung | Abgedeckt in |
|------------------|-------------|
| 6 eigenständige .adoc-Dateien | Tasks 3–8 |
| Schema: Szenario + Aufgaben + Tipps + Erfolgskontrolle | Tasks 3–8 |
| Konkrete Alltagsszenarien | Tasks 3–8 (Enkelin, Rezept, Treuhänder, Radio, Versicherung, Ausflug) |
| Beispieldateien für Modul 1 und 3 | Task 2 |
| Modul 5 baut auf Modul 3 auf | Task 7 |
| Modul 6 baut auf Modul 1 auf | Task 8 |
| Beispieldateien unter `docs/beispieldateien/` | Task 2 |
| Lizenzfreie Bilder (CC0) | Task 2 (Unsplash URLs) |
| Links im Kursprogramm | Task 9 |
