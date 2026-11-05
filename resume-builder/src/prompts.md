# Prompts log — Resume Builder (BSERC Week 2, Section A)

Prompts used, in order, as required by the Week 2 PDF. The sample person
throughout is the fake "Riya Sharma" (`riya@example.com`) from the PDF
preview — no real user data was used.

## 1. Build brief (from the parent agent / task)

> Build the BSERC Week 2 Section A project "Resume Builder" as a single
> self-contained HTML file with inline CSS/JS only — no external libraries,
> no network, no uploads, everything stays on the user's device.
>
> Context: BSERC GenAI Internship Week 2 (Bharat Space Education Research
> Centre, instructor Karan Bagul), PDF at
> `~/workspace/user/files/Week-2-Projects.pdf`. Working folder:
> `~/workspace/bserc-week2/section-a-resume-builder/`. A build plan
> already exists at `~/workspace/bserc-week2/plan.md` — read it first,
> and follow the PDF rules: test with fake/sample data only (use a sample
> person "Riya Sharma", riya@example.com, as in the PDF preview — never
> real user data), work offline, keep prompts in order.
>
> Requirements from the PDF (Resume Builder, Medium):
> 1. A form with a live A4 preview.
> 2. 3 templates that look truly different (e.g. clean classic, modern
>    two-column with sidebar, minimal bold-headings).
> 3. Drag sections into a new order (sections: Summary, Experience,
>    Education, Skills, Projects — use HTML5 drag-and-drop or up/down
>    buttons; drag-and-drop preferred).
> 4. Export as PDF — use the browser's Print to PDF (window.print with
>    @page A4 CSS) so the text stays selectable and copyable.
> 5. Save your data as a file (.json) and load it back.
>
> Done-when checklist (must all hold, verify by testing the file
> yourself): a long resume breaks cleanly across 2 pages; the text in
> the exported PDF can be selected and copied; loading the saved file
> restores everything; it works with Wi-Fi off.
>
> Deliverables: `index.html`, `README.md`, `prompts.md`,
> `test-checklist.md`. Verification before you finish: extract the
> `<script>` from index.html and run `node --check` on it; do NOT claim
> the PDF export was verified unless you actually verified it.

## 2. PDF check (agent step, before building)

Read `~/workspace/bserc-week2/plan.md` and the Resume Builder page of
`Week-2-Projects.pdf` (`pdftotext`) to confirm the five requirements and
the four "Done when" items matched the brief. They did — no change
needed to the brief.

## 3. Build `index.html`

Single file, inline CSS/JS only, system font stacks, no external
references. Implemented: live A4 preview; Classic / Modern / Minimal
templates; HTML5 drag-and-drop section ordering plus ↑/↓ buttons;
`window.print()` with `@page A4` and page-break rules; Save/Load
`.json` (plus localStorage autosave); long sample resume for
Riya Sharma (4 experience, 3 education, 4 projects, 13 skills).

## 4. Fix a bug found by the headless test — textarea `.type`

The jsdom test threw `TypeError: Cannot set property type of
HTMLTextAreaElement`. The form builder set `input.type = "text"` on
every field, including textareas. Fixed: only set `.type` for real
`<input>` elements.

## 5. Fix a bug found by the headless test — saved state shared by reference

`collectState()` returned `state.data` by reference, so editing the
resume after saving also changed the saved snapshot. Fixed: deep-clone
the data (`JSON.parse(JSON.stringify(...))`) in `collectState()`.

## 6. Fix Modern template pagination in print

WeasyPrint rendering of the Modern template produced 4 pages with two
blank pages — its grid sidebar does not fragment across pages. Fixed
with a print-only rule: in `@media print` the Modern body becomes a
block layout with a floated sidebar (`float: left; width: 62mm`) and the
main column beside it. Re-rendered: Modern is now 2 clean A4 pages.
Classic and Minimal were unaffected (Classic 2 pages, Minimal 3 pages
because its type is larger).

## 7. Upgrade brief — detailed Section A brief (parent agent, Nov 5 2026)

> Upgrade the existing Resume Builder in place to meet a detailed new
> brief. Do NOT rebuild from scratch. Add: (1) new editable sections
> Internships, Certifications, Achievements, Positions of
> Responsibility — in editor, preview, reorder list, PDF and save/load,
> with sample entries preloaded; (2) template names exactly "Modern
> professional", "Minimal ATS-friendly", "Contemporary technical";
> (3) character counter for the Professional Summary; (4) Reset and
> Clear with confirmation; (5) preview mode toggle; (6) mobile editing
> (stacked panels, no horizontal overflow); (7) keep live preview,
> multi-page flow, Print-to-PDF selectable text, JSON save/load of
> everything, drag-and-drop reorder. Test in at least 3 build-test-fix
> rounds; verify with `node --check` after every edit and zero
> external references by grep.

## 8. Template rename decision

The brief's names were mapped onto the three existing templates rather
than inventing three new layouts: "Modern" (two-column sidebar) →
**Modern professional** unchanged; "Minimal" (bold headings, large
name) → **Contemporary technical**; "Classic" (serif, centred) →
**Minimal ATS-friendly**, restyled to plain sans-serif, left-aligned,
single column — serif centred text is not ATS-friendly. Internal
template values renamed to `modern-professional` / `minimal-ats` /
`contemporary-technical` (CSS classes `tpl-…` renamed to match,
including the `@media print` sidebar rules). `applyState()` maps the
old values (`classic`/`modern`/`minimal`) to the new ones so
previously saved `.json` files still load. Default template is now
Modern professional.

## 9. New sections (one generic renderer, not four copies)

Added Internships, Certifications, Achievements, Positions of
Responsibility to: sample data (1 internship, 2 certifications,
2 achievements, 1 position), `SECTION_LABELS` / `DEFAULT_ORDER`
(now 9 sections), `sectionHtml()` (one branch each), the editor
(four new form blocks + add buttons), and `applyState()` (defaults
to `[]` for old files). `renderRepeatForms()` was refactored into a
`REPEAT_SECTIONS` config table + one generic renderer covering all
seven repeatable lists, so a future section is one config row.
`collectState()` already deep-clones `state.data`, so the new
sections are included in Save/Load automatically; Clear empties
them too.

## 10. Summary counter, Reset/Clear, preview mode, mobile CSS

- Summary counter: `updateSummaryCounter()` shows `n / 600
  characters` under the summary box and turns red with a warning
  past 600. It warns only — it never blocks typing.
- Reset: the old "Load sample resume" button is now "Reset to
  sample" and asks for confirmation (previously it reset with no
  warning). Clear already asked; both now confirm, and cancelling
  leaves the work untouched.
- Preview mode: toolbar button toggles `body.preview-mode` (editor
  hidden, preview centred, other toolbar buttons hidden); the button
  becomes "Exit preview" and Esc also exits.
- Mobile: under 900px the layout stacks to one column and the A4
  sheet becomes `width: 100%`; under 600px form rows and the
  template picker go single-column. Verified by CSS inspection;
  a real-phone check is still listed as to-verify.

## 11. Bug found during the upgrade — summary `maxlength` would truncate loads

I first added `maxlength="1200"` to the summary textarea. On
re-check I realised a loaded `.json` whose summary was longer could
be silently truncated by the browser when the value was assigned.
Removed the attribute: the counter warns past 600 but nothing
truncates. Verified afterwards: a 1500-character summary loaded
from JSON keeps its full length and the counter shows
"1500 / 600 characters — over the recommended length".

## 12. Test rounds for the upgrade

- **Round 1 — new sections + save/clear/load:** jsdom, 24
  assertions. Added an entry to each new section via the + buttons,
  typed into one, reordered Certifications to the top with the ↑
  buttons, switched to Minimal ATS-friendly, saved, cleared
  (everything emptied, counter back to 0), loaded the JSON back
  (template, order, all entries restored), and loaded a legacy
  `classic`-template file (mapped to Minimal ATS-friendly, new
  sections defaulted to empty, no crash). First run: the reorder
  check failed — my test held a stale DOM node after the list
  re-rendered; that was a test bug, fixed by re-querying the row
  each click, and the app itself was correct.
- **Round 2 — pagination in all 3 templates:** jsdom rendered the
  full 9-section sample per template; WeasyPrint rendered each to
  PDF. All three: **3 A4 pages** (the resume grew from 5 to 9
  sections), text extractable with `pdftotext`, every new section
  present in the PDF text, no page starting or ending with a
  stranded heading (page 2 starts with EDUCATION in all three;
  page 3 starts with a section heading).
- **Round 3 — counter, confirm, preview mode, reorder, templates,
  mobile CSS:** jsdom, 18 assertions. Counter warns at 650 chars
  and is normal at 14; Reset with confirm denied keeps edits, with
  confirm accepted restores the sample; preview mode hides the
  editor and Esc exits it; drag-and-drop (stubbed dataTransfer)
  moves Achievements to the top and the preview follows; all three
  templates apply with all sections present; Modern professional
  sidebar still holds Skills; mobile/preview/page-break CSS rules
  confirmed present by inspection; zero external references.
  Total across rounds 1 + 3: **42 assertions, all passing**;
  `node --check` passes after every edit.

