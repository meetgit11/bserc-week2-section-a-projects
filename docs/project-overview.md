# Project overview — BSERC Week 2, Section A

## Context

Bharat Space Education Research Centre (BSERC) GenAI Internship, Week 2 (15-day GenAI Internship 2026, instructor Karan Bagul). Week 2 is about building apps, tools and extensions with an AI agent. Section A ("Web tools") asks for at least two useful web applications that run in a browser, keep files on the user's own laptop, and need no account, no paid API and no internet connection.

This repository contains the two selected Section A projects. It is an independently developed student project; BSERC does not officially endorse it.

## Objectives

1. Build a tool that prepares photographs and signatures for examination, university and job application forms, matching each form's real dimension and file-size rules.
2. Build a free, offline-first resume builder with a live A4 preview, three genuinely different templates, rearrangeable sections and PDF export.
3. Make both tools genuinely functional (not visual prototypes), fully offline, and private by design — no uploads, no accounts, no paid services.
4. Document everything: requirements, architecture, testing (including what could not be verified here) and the prompts used to build it.

## The two applications

### Form Photo Fixer (`form-photo-fixer/`)

A browser-based tool that prepares photographs and signatures for application forms.

- Upload photo and signature (JPEG/JPG/PNG, validated, with original dimensions and size shown).
- 4 form presets with documented official sources (JEE Main, Indian Passport, OCI/e-Visa, Exam Form A) + user-defined custom presets that persist in the browser.
- Interactive cropping: drag to pan, zoom, crop-size control, face-position guide, aspect ratio locked to the preset.
- Compression to just under the selected file-size limit, with live progress and an original-vs-processed comparison.
- Downloads with meaningful filenames and a final ready/not-ready validation status.

### Resume Builder (`resume-builder/`)

A browser-based resume creation tool with a live A4 preview.

- Form with live A4 preview: personal information, summary, experience, internships, education, skills, projects, certifications, achievements, positions of responsibility.
- Three templates with the brief's exact names: Modern professional, Minimal ATS-friendly, Contemporary technical.
- Drag-and-drop section reordering (plus ↑/↓ buttons), preserved in the preview and the PDF export.
- Summary character counter (600 recommended, warning only), Reset/Clear with confirmation, preview mode, mobile editing layout.
- PDF export via the browser's Print to PDF (selectable text); save/load as a `.json` file; localStorage autosave.

## Architecture

Both applications share the same deliberately simple architecture:

```
Browser only — no server, no backend, no database
┌──────────────────────────────────────────────────┐
│  Single HTML file per app                        │
│  ┌────────────┐  ┌──────────────┐  ┌──────────┐  │
│  │  HTML/CSS  │  │  JavaScript  │  │ Browser  │  │
│  │  (inline)  │  │  (inline)    │  │ APIs     │  │
│  │            │  │              │  │ Canvas / │  │
│  │            │  │              │  │ File-    │  │
│  │            │  │              │  │ Reader / │  │
│  │            │  │              │  │ local-   │  │
│  │            │  │              │  │ Storage /│  │
│  │            │  │              │  │ print()  │  │
│  └────────────┘  └──────────────┘  └──────────┘  │
└──────────────────────────────────────────────────┘
```

- **One file per app.** Each app is a single HTML file with inline CSS and JavaScript. There is no build step, no bundler, no framework and no `package.json`.
- **Browser-native APIs only.** Canvas + FileReader for image processing (Form Photo Fixer); `window.print()` for PDF export; `localStorage` for autosave and custom presets; Blob + anchor download for files.
- **No external dependencies.** Zero external references (no CDN, web fonts, images or scripts). System font stacks only. Both apps work with Wi-Fi off.
- **No data leaves the device.** There is no server code, no analytics and no network request in either app.
- **Independent apps.** The two projects share nothing; each can be opened, used and submitted on its own.

## Technology stack

| Layer | Choice |
|---|---|
| Markup / styling / logic | HTML5, CSS3, JavaScript (inline in one file per app) |
| Image processing | Canvas API (crop, resize, JPEG quality stepping) |
| File handling | FileReader (upload), Blob + `URL.createObjectURL` (download) |
| Persistence | `localStorage` (autosave, custom presets) |
| PDF export | Browser Print to PDF (`window.print()`, `@page A4`) |
| Testing (build environment) | `node --check`, jsdom harnesses, WeasyPrint rendering |
| Version control / publishing | Git + GitHub (this repository) |
| Deployment (planned) | Vercel (static hosting of the two HTML files) |

No backend, no database, no paid API, no external service.

## How to run

There is nothing to install:

1. Clone or download this repository.
2. Open `form-photo-fixer/index.html` or `resume-builder/index.html` in any modern browser (Chrome / Edge / Firefox) — double-click works.
3. To test offline: turn Wi-Fi off and reload the page.

## Repository structure

```
bserc-week2-section-a-projects/
├── README.md, prompts.md, LICENSE
├── form-photo-fixer/   (index.html at root + src/ + public/sample-photo.jpg)
├── resume-builder/     (index.html at root + src/ + public/sample-resume.json)
└── docs/               (this folder)
```

## Status (5 Nov 2026)

Both apps are built, upgraded to the detailed Section A brief, and tested headlessly as documented in `testing-report.md`. The remaining checks all need the author's machine (real Chrome Print-to-PDF, large-phone-photo end-to-end test, real-phone layout check). Nothing has been pushed to GitHub yet — this folder is the local assembly; the public repository `bserc-week2-section-a-projects` still has to be created on github.com.
