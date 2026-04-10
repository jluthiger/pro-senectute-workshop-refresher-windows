# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a documentation-only repository for a Pro Senectute Windows 11 refresher workshop for seniors. All content is in German. Source files are written in AsciiDoc (`.adoc`) and stored in `docs/`. The CI/CD pipeline converts them to HTML and deploys to GitHub Pages.

## Build

The build is automated via GitHub Actions on push to `main`. To build locally:

```bash
mkdir -p build
find docs -name "*.adoc" -exec asciidoctor -D build {} \;
cp docs/*.pdf build
```

Requires `asciidoctor` to be installed (`sudo apt-get install -y asciidoctor` on Ubuntu, `brew install asciidoctor` on macOS).

## Repository Structure

- `docs/` — AsciiDoc source files (`.adoc`) and PDF handouts
- `.github/workflows/asciidoctor.yml` — Builds and deploys to GitHub Pages on push to `main`
- `build/` — Generated HTML output (not committed, produced by CI)

## Content Structure

Workshop spans 2 days (3 hours each, 09:00–12:00) and covers:

### Day 1

#### Focus on "Files and Practical Windows Features"

- Datei-Explorer and Ordnerstrukturen (file explorer and folder organization)
- Zwischenablage (clipboard, including history with Win+V)
- Screenshots (Snipping Tool, Win+Shift+S)
- PDF-Dateien (create via Print to PDF, merge)
- ZIP-Dateien (compress and extract)

### Day 2

#### Focus on "Programs, OneDrive and Collaboration"

- Programme installieren (Microsoft Store, safe downloads, uninstall)
- OneDrive einrichten und nutzen (cloud storage, automatic backup)
- Dokumente teilen und gemeinsam bearbeiten (sharing links, Word Online co-editing)

## License

CC0 1.0 Universal — content is dedicated to the public domain.
