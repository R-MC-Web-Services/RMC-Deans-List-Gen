# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Two standalone, client-side HTML tools for Randolph-Macon College's Dean's List workflow. No build step, no server, no package manager — each file is self-contained and can be opened directly in a browser or hosted statically.

- **`graphics-gen.html`** — Generates individualized certificate PNG images from a template + student roster, then bundles them into a downloadable ZIP.
- **`table-gen.html`** — Generates WordPress-embeddable HTML/CSS/JS snippets for a searchable Dean's List directory with certificate modals.

## Running Locally

Open either HTML file directly in a browser. No build or install step required.

## Architecture

### graphics-gen.html — Certificate Generator

**Data flow:**
1. User uploads a template PNG and an Excel/CSV roster
2. XLSX.js parses the roster into `(First, Last, Major)` rows
3. For each student, a Canvas element renders the template with:
   - Student name in **meno-display** (Adobe Fonts/TypeKit, 150pt, gold `#ffcf00`)
   - Major in **Red Hat Display** (Google Fonts, 78pt bold, white)
   - Text centered, positioned at ~78% down the canvas
4. Each canvas is exported to a PNG blob, collected by JSZip, and downloaded as a single ZIP
5. Font loading is awaited via `document.fonts.ready` before processing begins

**Naming convention:** Output files are `{prefix}-{FirstLast}.png` where prefix comes from the "Filename Prefix" input (e.g., `Spring26-JaneDoe.png`).

### table-gen.html — Table Generator

**Data flow:**
1. User uploads an Excel/CSV roster and enters the CDN image path prefix and filename prefix
2. Students are serialized as a `window.deansListStudents` JSON array with image URLs constructed as `{imagePath}{prefix}-{FirstLast}.png`
3. The tool outputs **four copyable code blocks** to paste into WordPress:
   - **CSS** (one-time): search box, table, modal, social share button styles
   - **Modal HTML** (one-time): modal skeleton with social share links
   - **JavaScript** (one-time): search/filter, modal open/close, deep linking via `?student=firstname-lastname`, social sharing
   - **Table HTML** (per semester): `<script>` tag with the student array + `<table>` markup

**Certificate image URL pattern:** `{imagePath}{prefix}-{FirstLast}.png`
The default image path placeholder is `https://res.cloudinary.com/dn07gk4tk/image/upload/`.

## External Dependencies (CDN-loaded)

| Library | Version | Purpose |
|---|---|---|
| XLSX.js | 0.18.5 | Excel/CSV parsing |
| JSZip | 3.10.1 | ZIP file creation |
| Adobe Fonts (TypeKit) | — | meno-display typeface for certificate names |
| Google Fonts | — | Red Hat Display (major text), Roboto (UI) |

All dependencies load from CDN at runtime — no local copies.

## RMC Brand Colors

- Gold: `#ffcf00`
- Black: `#000`

## Semester Templates

Template PNG files are stored in the repo root (`fall2025-template.png`, `spring2026-template.png`). When updating for a new semester, add the new template PNG and update the default prefix placeholder in `graphics-gen.html`.
