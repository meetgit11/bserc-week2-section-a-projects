# Test checklist — Resume Builder

The four "Done when" items from the Week 2 PDF, plus the completion
criteria from the detailed Section A brief (Nov 5, 2026). Each item is
ticked only for what was actually verified. Sample data throughout:
fake person "Riya Sharma".

## 1. A long resume breaks cleanly across 2+ pages

- [x] **Verified — with a stated method.** No browser is installed in
  the build VM, so the page was rendered headlessly: jsdom ran the
  file's JavaScript to build the preview, and WeasyPrint rendered that
  output to PDF using the file's own `@media print` / `@page A4` CSS.
  - The full sample resume now has **9 sections** (the upgrade added
    Internships, Certifications, Achievements, Positions of
    Responsibility), so it is longer than the original 5-section
    sample: **3 A4 pages in all three templates** (Modern
    professional, Minimal ATS-friendly, Contemporary technical),
    595.28 × 841.89 pt.
  - No page starts or ends with a stranded heading: page 1 starts
    with the name; page 2 starts with the EDUCATION heading in all
    three templates; page 3 starts with a section heading
    (POSITIONS OF RESPONSIBILITY / CERTIFICATIONS / PROJECTS).
    Pages end mid-content at complete entries.
  - CSS rules in place: `break-after: avoid` on headings,
    `break-inside: avoid` on sections and entries, `orphans/widows: 3`.
  - (Before the upgrade the 5-section sample was 2 pages in Classic
    and Modern, 3 in Minimal — see the earlier entries in
    `prompts.md`.)
- [ ] **Still to do by the user:** one real check in Chrome's print
  preview (Export PDF) to confirm the page count there, since the
  verification above used WeasyPrint, not Chrome's print engine.

## 2. The text in the PDF can be selected and copied

- [x] **Verified in the rendered PDF.** `pdftotext` extracted the full
  text from all three WeasyPrint PDFs: name, contact line, Summary,
  all Experience / Internships / Education entries, Skills, Projects,
  Certifications ("Google Data Analytics Professional Certificate",
  "SQL for Data Science"), Achievements ("Hackathon finalist…") and
  Positions of Responsibility ("Technical Lead") are present as real
  text, not images. The export path is the browser's Print to PDF
  (`window.print()`), which produces real text by design; no
  canvas/image export is used anywhere in the file.
- [ ] **Still to do by the user:** select and copy a line from the
  PDF produced by Chrome's Print to PDF, as the final browser check
  (see item 1).

## 3. Loading the saved file restores everything

- [x] **Verified headlessly (jsdom, Round 1: 24 assertions, all
  passing).** The test: added an entry to each of the four new
  sections via the + buttons, typed into one, reordered
  Certifications to the top, switched template to Minimal
  ATS-friendly, saved with `collectState()` (the same object Save as
  .json downloads), cleared everything, then loaded the JSON back
  with `applyState()` (the same function Load .json… uses) and
  confirmed the template, the reordered order, all fields and all
  new-section entries (2 internships, 3 certifications,
  3 achievements, 2 positions) came back.
- [x] **Legacy files still load.** A file saved with the old template
  name `classic` loads as Minimal ATS-friendly, gains empty new
  sections, and does not crash. Old files without the new arrays get
  `[]` defaults.
- [x] **Also verified by hand in the code:** Save downloads
  `<name>-resume.json` via a Blob; Load reads it with FileReader,
  validates it, and rebuilds every form field, the template picker,
  the order list and the preview (`syncEditor()`). `collectState()`
  deep-clones the data, so later edits cannot corrupt a saved
  snapshot (a bug fixed in the first build).

## 4. It works with Wi-Fi off

- [x] **Verified by inspection.** The file contains zero external
  references: a search of `index.html` for `http://`, `https://`,
  `src=`, `href=`, `<img`, `<link` and `@import` returns nothing.
  All CSS is inline, all JavaScript is inline, all fonts are system
  font stacks. No CDN, no web font, no image, no network request is
  made. The jsdom tests also ran with no network access to the file.
- [ ] **Still to do by the user:** reload the page once with Wi-Fi off
  on their own laptop/phone as the final real-world check.

## 5. New sections (detailed brief)

- [x] **Internships, Certifications, Achievements, Positions of
  Responsibility** each have an editor block with add/remove, appear
  in the live preview, in the 9-item reorder list, in the exported
  PDF (verified in the WeasyPrint text, item 2), and in save/load
  (Round 1). The sample preloads 1 internship, 2 certifications,
  2 achievements and 1 position, so all four are visibly working on
  first load.

## 6. Three templates with the brief's exact names

- [x] **Verified headlessly.** The picker offers exactly "Modern
  professional", "Minimal ATS-friendly", "Contemporary technical".
  Modern professional keeps the two-column sidebar (Skills in the
  sidebar); Minimal ATS-friendly is plain single-column with no
  sidebar or graphics; Contemporary technical has the large bold name
  and extra-bold headings. All three apply and render in the preview
  and in the WeasyPrint PDFs. Mapping from the old names is
  documented in `README.md`.

## 7. Summary character counter

- [x] **Verified headlessly.** Typing 650 characters shows
  "650 / 600 characters — over the recommended length" and the
  counter turns red; 14 characters shows "14 / 600 characters"
  with no warning. A 1500-character summary loaded from JSON keeps
  its full length (nothing truncates it) and the counter warns.

## 8. Reset and Clear with confirmation

- [x] **Verified headlessly.** With `confirm()` returning false,
  Reset leaves the edited name untouched; with it returning true,
  Reset restores the sample (name, template, all sections). Clear
  empties every field including the new sections and resets the
  counter to 0. Both buttons ask before acting.

## 9. Preview mode

- [x] **Verified headlessly.** Clicking Preview mode adds
  `body.preview-mode` (editor hidden by CSS), the button becomes
  "Exit preview", the preview stays visible; pressing Esc exits
  preview mode and restores the button label.

## 10. Reordering changes the exported resume

- [x] **Verified headlessly.** ↑/↓ buttons moved Certifications to
  the top and the order persisted through save/load; a simulated
  drag-and-drop moved Achievements to the top and the preview
  heading order followed (Achievements before Summary). The PDF
  renders sections in `state.order` order.

## 11. Mobile editing

- [x] **Verified by CSS inspection.** Under 900px the layout stacks
  to one column and `.resume` becomes `width: 100%` (no fixed
  210mm on a phone); under 600px form rows and the template picker
  go single-column; the toolbar wraps and is not sticky on mobile.
  No fixed-width element wider than the viewport remains in the
  stylesheet.
- [ ] **Still to do by the user:** open the file on a real phone and
  confirm there is no horizontal overflow and the controls are
  usable — no browser or phone emulator exists in this VM.

## Other checks run

- `node --check` on the extracted `<script>`: **passes** (re-run
  after every edit in the upgrade).
- jsdom tests, Rounds 1 + 3: **42 assertions, all passing** (24 in
  Round 1, 18 in Round 3). One Round 1 failure was a test bug
  (stale DOM node after re-render), fixed in the test, not the app.
- WeasyPrint, Round 2: **3 A4 pages in each of the 3 templates**,
  all text extractable.
- File size: `index.html` is ~40 KB (was ~30 KB), single file.

## Honest summary

All four PDF "Done when" items and all of the detailed brief's
completion criteria hold as far as they can be verified without a
browser in this VM. The remaining gaps are all on the user's machine
and take a few minutes: a real Chrome Print-to-PDF check (page count
+ select/copy text + Wi-Fi-off reload) and opening the file on a real
phone to confirm the mobile layout. Both are flagged in `README.md`
rather than claimed here.
