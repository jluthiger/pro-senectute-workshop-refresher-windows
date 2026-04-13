# Design: Praktische Übungen für den Windows 11 Refresher Workshop

**Datum:** 2026-04-13  
**Projekt:** Pro Senectute – Windows 11 Refresher Workshop  
**Sprache:** Deutsch  
**Status:** Genehmigt

---

## Ziel

Für jedes der 6 Module des zweitägigen Workshops wird eine eigenständige AsciiDoc-Datei mit einer praktischen Übung erstellt. Die Übungen ergänzen die bestehenden Praxis-Abschnitte im Kursprogramm (`docs/kursprogramm.adoc`) mit einem konkreten Alltagsszenario, das Senioren anspricht.

---

## Aufbau jeder Übungsdatei (Ansatz A)

Jede Datei folgt demselben Schema:

1. **Titel** — Modulnummer und Thema
2. **Szenario-Einleitung** — 2–3 Sätze, die eine konkrete Alltagssituation schildern
3. **Aufgaben** — nummerierte Liste; zu schwierigen Schritten gibt es eine eingebettete Tipp-Box (`[TIP]`)
4. **Erfolgskontrolle** — «Das haben Sie geschafft, wenn...» — gibt Teilnehmenden ein klares Erfolgserlebnis

---

## Dateien

| Dateiname | Modul | Thema |
|-----------|-------|-------|
| `docs/uebung-modul-1.adoc` | 1 | Datei-Explorer und Ordnerstrukturen |
| `docs/uebung-modul-2.adoc` | 2 | Zwischenablage und Screenshots |
| `docs/uebung-modul-3.adoc` | 3 | PDF- und ZIP-Dateien |
| `docs/uebung-modul-4.adoc` | 4 | Programme installieren |
| `docs/uebung-modul-5.adoc` | 5 | OneDrive optimal einsetzen |
| `docs/uebung-modul-6.adoc` | 6 | Dokumente teilen und gemeinsam bearbeiten |

---

## Szenarien

### Modul 1 — Datei-Explorer und Ordnerstrukturen
**Szenario:** Enkelin Clara hat Ihnen Fotos von ihren Sommerferien geschickt. Die Bilder sind alle ungeordnet im Download-Ordner gelandet. Sie legen eine sinnvolle Ordnerstruktur an und sortieren die Dateien.  
**Benötigt:** `kurs-dateien-modul1.zip` (6 Ferienfotos + `Packliste-Familie.docx`)

### Modul 2 — Zwischenablage und Screenshots
**Szenario:** Sie haben im Internet ein wunderbares Kuchenrezept gefunden. Sie wollen den Rezepttext in eine Textdatei kopieren und einen Screenshot der Webseite erstellen, um ihn einer Freundin per E-Mail zu schicken.  
**Benötigt:** Kein Download — Teilnehmende nutzen eine beliebige Rezept-Webseite.

### Modul 3 — PDF- und ZIP-Dateien
**Szenario:** Ihr Treuhänder benötigt einige Unterlagen von Ihnen. Sie sollen einen Brief als PDF speichern und zusammen mit zwei Belegen als ZIP-Datei vorbereiten.  
**Benötigt:** `kurs-dateien-modul3.zip` (Vorlage `Brief-an-Treuhänder.docx` + 2 PDF-Belege)

### Modul 4 — Programme installieren
**Szenario:** Sie möchten am PC Radio hören. Im Microsoft Store suchen Sie nach einer geeigneten App, installieren diese, probieren sie aus — und deinstallieren sie anschliessend wieder.  
**Benötigt:** Kein Download — Microsoft Store wird direkt genutzt.

### Modul 5 — OneDrive optimal einsetzen
**Szenario:** Nach der Übung in Modul 3 wollen Sie sicherstellen, dass Ihre wichtigen Dokumente nicht verloren gehen. Sie speichern die Unterlagen aus Modul 3 in OneDrive und überprüfen, ob sie im Browser sichtbar sind.  
**Benötigt:** Dateien aus Modul 3 (kein neuer Download).

### Modul 6 — Dokumente teilen und gemeinsam bearbeiten
**Szenario:** Sie planen einen Familienausflug und haben in Modul 1 bereits eine Packliste als Word-Dokument erhalten. Jetzt teilen Sie dieses Dokument mit Ihrer Tochter und bearbeiten es gemeinsam in Word Online.  
**Benötigt:** `Packliste-Familie.docx` aus Modul 1 (kein neuer Download).

---

## Beispieldokumente

Zwei ZIP-Archive werden in `docs/beispieldateien/` abgelegt und sind über GitHub Pages zugänglich.

### `kurs-dateien-modul1.zip`
- 6 lizenzfreie Ferienfotos (Pixabay/Unsplash, CC0), benannt mit realistischen, unsortieren Namen
- `Packliste-Familie.docx` — einfache Word-Vorlage einer Packliste

### `kurs-dateien-modul3.zip`
- `Brief-an-Treuhänder.docx` — Word-Vorlage eines kurzen Briefes
- `Beleg-Krankenkasse.pdf` — einfaches Beispiel-PDF
- `Beleg-Strom.pdf` — einfaches Beispiel-PDF

### Lizenz
Alle Beispieldateien stehen unter CC0 1.0 Universal (Public Domain), passend zur Projektlizenz.

---

## Abhängigkeiten zwischen Modulen

```
Modul 1 ──── liefert Packliste-Familie.docx ──▶ Modul 6
Modul 3 ──── liefert Brief + Belege ──────────▶ Modul 5
```

---

## Nicht im Scope

- Keine Musterlösungen oder Lehrerdokumente
- Keine automatisierten Tests
- Keine englische Übersetzung
