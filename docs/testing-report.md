# Testing report — BSERC Week 2, Section A

How both applications were tested, what passed, what failed and was fixed, and what is still unverified. All testing used fake/sample data only, as the Week 2 PDF requires. No browser (Chrome/Firefox) is installed in the build environment, so every "verified here" item below was verified headlessly (jsdom harnesses, `node --check`, WeasyPrint rendering, `pdftotext`, grep) — and every item that needs a real browser or phone is listed as **still to verify**, not claimed as passed.

## Method

Per the detailed brief (§6, Phase 4): at least three meaningful build-test-fix rounds per application, testing normal inputs, invalid inputs, large files, long content, multiple-page output, mobile responsiveness, offline functionality, browser refresh/persistence and all interactive controls. Issues found were fixed rather than merely documented.

- **Form Photo Fixer:** jsdom harness (`/tmp/fpf-test.js` + `/tmp/fpf-test-round3.js`) with stubbed canvas + image decoding, plus `node --check` after every edit and grep for external references.
- **Resume Builder:** jsdom harnesses for interaction tests, WeasyPrint rendering of the JavaScript-built page for pagination/PDF checks, `pdftotext` for selectable-text checks, `node --check` after every edit, grep for external references.

## Form Photo Fixer — results

### Rounds and results

| Round | What was tested | Result |
|---|---|---|
| Round 1 (jsdom, 69 assertions) | Non-image + PNG + sample uploads; validation and original-size display; resize crop window; process at each preset and check output px/KB and comparison view; add a custom preset, reload, check persistence, delete it | 66 passed, **3 failed** — see "Bugs found and fixed" |
| Round 2 (fix + full re-run) | The 3 Round 1 failures fixed, full harness re-run | **69 passed, 0 failed** |
| Round 3 (edge cases, jsdom, 11 assertions) | PNG output format (no quality setting, saved = 0, honest note); invalid custom preset (max ≤ min) rejected; signature crop-size slider; zoom label; reset restores zoom + crop size; toasts auto-dismiss after 4 s | **11 passed, 0 failed** |
| **Total (final state)** | | **80 assertions passing** |

### What passed (with evidence)

- `node --check` on the extracted script: passes (re-run after every edit).
- Zero external references: grep for `http(s)://` returns none; system fonts only; zero `alert()` calls left.
- Upload validation: `.txt` and `.gif` rejected with a clear on-page error + error toast; corrupt images rejected; Process button disabled after any failed load.
- Original dimensions + size on upload for photo and signature (name, px, KB, type).
- Resizable crop area (50–100% slider) keeping the preset aspect ratio and the face guide inside it; Reset restores zoom 1.00× and crop size 100%.
- Compression to just under the limit at **all 4 presets**, output px/KB inside each preset's min–max range: JEE 350×450 (10–200 KB), Passport 630×810 (10–250 KB), OCI 600×600 (10–300 KB), Exam Form A 350×450 (10–50 KB).
- Original-vs-processed comparison, compression progress line, toasts, downloads with meaningful filenames.
- Custom presets: persist in localStorage across a fresh page load, delete removes them from UI + localStorage; invalid presets rejected.

### Bugs found and fixed

1. **"KB saved" was negative for JEE.** The comparison "original" was a PNG re-encode, which can be smaller than a high-quality JPEG for flat graphics. Fixed by changing the baseline to the same crop at JPEG quality 100% and relabelling it "Original (cropped, best quality)".
2. **OCI preset checks read the Passport preset's values.** The harness matched preset cards by whole-card text, and the Passport card's source text mentions OCI. Fixed the harness to match on the card's title (test bug, not an app bug).
3. **Stale image could be processed after a failed upload.** A real app bug: after a failed/corrupt upload the Process button stayed enabled. Fixed — it is now disabled on any load error.

### Still to verify (user's machine)

- Open `index.html` with Wi-Fi off, upload a **large phone photo** (several MB), process it for the JEE preset, confirm the downloaded file's properties show 350×450 px and ≤ 200 KB.
- Same for the Passport preset (630×810 px, ≤ 250 KB); check the face sits inside the oval in a real browser.
- Confirm the toasts, crop-size slider and comparison view behave the same in real Chrome as in the jsdom harness (the harness stubs canvas decoding).
- Confirm the official notices named in the README still match the current cycle before submitting a real form.

## Resume Builder — results

### Rounds and results

| Round | What was tested | Result |
|---|---|---|
| First build (jsdom, 21 assertions) | Sample renders, live typing updates preview, all 3 templates apply, reorder works, save/load round-trip restores name, fields, template and order | **21 passed, 0 failed** (2 bugs found and fixed during it — see below) |
| Upgrade Round 1 (jsdom, 24 assertions) | New sections (add entry to each, type into one), reorder Certifications to the top, switch to Minimal ATS-friendly, save, clear, load back, legacy `classic`-template file | 23 passed, 1 failed on first run — a **test bug** (stale DOM node after re-render), fixed in the test; app was correct. Re-run: **24 passed** |
| Upgrade Round 2 (pagination, WeasyPrint) | Full 9-section sample per template rendered to PDF | **3 A4 pages in all three templates**, all text extractable with `pdftotext`, every new section present, no stranded headings |
| Upgrade Round 3 (jsdom, 18 assertions) | Summary counter (warns at 650, normal at 14; 1500-char load not truncated), Reset/Clear confirm dialogs, preview mode + Esc, drag-and-drop reorder, all 3 templates, mobile/preview/page-break CSS, zero external references | **18 passed, 0 failed** |
| **Total (upgrade rounds 1 + 3)** | | **42 assertions, all passing** |

### What passed (with evidence)

- `node --check` on the extracted script: passes (re-run after every edit).
- Zero external references: search for `http://`, `https://`, `src=`, `href=`, `<img`, `<link`, `@import` returns nothing.
- Long resume (9 sections) breaks cleanly across **3 A4 pages in all three templates** (595.28 × 841.89 pt); page 2 starts with the EDUCATION heading in all three; page 3 starts with a section heading; pages end mid-content at complete entries.
- PDF text selectable: `pdftotext` extracted the full text from all three PDFs (name, contact, summary, all entries, skills, certifications, achievements, positions) — real text, not images.
- Save/load round-trip: template, reordered order, all fields and all new-section entries restored after save → clear → load; legacy template names map correctly.
- Summary counter, Reset/Clear confirmations, preview mode (incl. Esc), drag-and-drop + ↑/↓ reorder, mobile CSS (stacked panels under 900px, single-column under 600px, no fixed-width overflow).

### Bugs found and fixed

1. **Textarea `.type` crash.** The form builder set `input.type = "text"` on every field, including textareas (`TypeError`). Fixed: only set `.type` for real `<input>` elements.
2. **Saved state shared by reference.** `collectState()` returned `state.data` by reference, so later edits corrupted the saved snapshot. Fixed: deep-clone in `collectState()`.
3. **Modern template printed 4 pages with 2 blank pages.** Its grid sidebar does not fragment across pages. Fixed with a print-only rule (block layout + floated sidebar in `@media print`); re-rendered to 2 clean pages (before the upgrade; 3 pages after the 9-section upgrade).
4. **Summary `maxlength` would truncate loads.** A `maxlength="1200"` attribute could silently truncate a longer summary loaded from JSON. Removed; the counter warns past 600 but nothing truncates (verified: a 1500-character summary keeps its full length).

### Sample JSON verification (repo assembly)

The sample resume shipped in this repo (`resume-builder/public/sample-resume.json`) was verified against the app's own save format by loading it into the app in jsdom: name, email, template (`modern-professional`), 9-section order, internship, certification, achievement, position and skills all appear in the preview; the loaded data is deep-equal to the file's data (a strict string comparison differs only in object key order, which `applyState` rebuilds in its own fixed order — content is identical). 12/13 jsdom checks passed outright; the 13th is that key-order-only difference, confirmed identical by a deep-equal check.

### Still to verify (user's machine)

- One real Chrome **Export PDF (Print to PDF)** check: page count in Chrome's print preview, and select/copy a line of text from the produced PDF.
- Reload the page once with Wi-Fi off on the user's laptop/phone.
- Open the file on a real phone and confirm no horizontal overflow and usable controls.
- Confirm the on-screen "Preview ≈ N A4 pages" label against the print preview's page count (the label is an estimate; the print preview is authoritative).

## Known limitations (both apps)

- The jsdom harnesses stub canvas decoding, so real-browser rendering is the final check for both apps.
- Form Photo Fixer does not retouch photos or change backgrounds and cannot guarantee a form will accept a photo; form rules change every cycle.
- Resume Builder's PDF export prints the preview as-is and does not shrink text to fit one page; the Modern professional template always puts Skills in the sidebar by design.
- No end-to-end browser automation (e.g. Playwright) was available in the build environment; the real-browser checks above are the substitute.
