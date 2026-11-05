# Resume Builder — requirements traceability

Every mandatory feature from the detailed Section A brief (5 Nov 2026), whether it is implemented, and how it was tested. Sources: `resume-builder/src/test-checklist.md` and `resume-builder/src/prompts.md`. "Verified here" means verified in the build environment with jsdom harnesses and WeasyPrint rendering (sample data only); "to verify on the user's machine" means it needs a real browser/phone and is **not** claimed as passed.

## 1. Resume editor

| Section | Implemented? | Notes |
|---|---|---|
| Personal information (name, title, email, phone, location, link) | Yes | Single fields in the form; shown in the resume header |
| Professional summary | Yes | Textarea + live character counter (see §7) |
| Education | Yes | Repeatable entries (degree, school, dates, description), add/remove |
| Skills | Yes | Comma-separated list |
| Projects | Yes | Repeatable entries (name, description, link), add/remove |
| Work experience | Yes | Repeatable entries (role, company, dates, description), add/remove |
| Internships | Yes | Repeatable entries, same fields as experience; 1 sample entry preloaded |
| Certifications | Yes | Repeatable entries (title, issuer, year, detail); 2 sample entries preloaded |
| Achievements | Yes | Repeatable entries (title, year, detail); 2 sample entries preloaded |
| Positions of responsibility | Yes | Repeatable entries (title, organisation, year, detail); 1 sample entry preloaded |
| Contact information | Yes | Email, phone, location, link — part of personal information, shown in the header/contact line |

All seven repeatable lists use one generic renderer (a `REPEAT_SECTIONS` config table), so every list behaves the same way.

## 2. Live A4 preview

| Requirement | Implemented? | How tested |
|---|---|---|
| Accurate A4 page preview | Yes | Preview sheet is A4-proportioned; print CSS is `@page { size: A4; margin: 0 }` |
| Updates as the user edits | Yes | Preview re-renders on every edit (verified in jsdom: typing updates the preview) |
| Readable typography and professional spacing | Yes | Three templates, each with its own type scale and spacing |
| Handle long resumes across multiple pages | Yes | The full 9-section sample flows to **3 A4 pages in all three templates** (WeasyPrint rendering) |
| Prevent content from being cut off between pages | Yes | `break-after: avoid` on headings, `break-inside: avoid` on sections and entries, `orphans/widows: 3`; no page starts or ends with a stranded heading (page 2 starts with the EDUCATION heading in all three templates) |

## 3. Three genuinely different templates

Template names are exactly the ones in the detailed brief. Mapping from the first build's working names is documented in the app README (old saved files still load).

| Template (brief name) | Layout / typography / hierarchy | Verified? |
|---|---|---|
| **Modern professional** | Dark header, two-column layout with a sidebar. Skills sit in the sidebar by design; the other eight sections follow the dragged order in the main column. | Yes — applies, renders, prints to 3 A4 pages |
| **Minimal ATS-friendly** | Plain single column, no sidebar, no graphics, no tables, left-aligned, simple headings — deliberately plain so applicant tracking systems can parse it. | Yes — applies, renders, prints to 3 A4 pages |
| **Contemporary technical** | Large bold name, extra-bold uppercase headings, wide spacing, no sidebar. | Yes — applies, renders, prints to 3 A4 pages |

Each template has a distinctly different layout, typography and visual hierarchy; the template picker shows all three, and switching re-renders the preview immediately.

## 4. Reorder sections

| Requirement | Implemented? | How tested |
|---|---|---|
| Drag and drop resume sections | Yes | HTML5 drag-and-drop on all 9 sections (⋮⋮ handle) in the "Section order" list; a simulated drag moved Achievements to the top and the preview heading order followed |
| ↑ / ↓ buttons as well (useful on a phone) | Yes | Buttons move a section up/down; verified in jsdom (Certifications moved to the top) |
| The exported resume must preserve the chosen order | Yes | The PDF renders sections in `state.order` order; the order also persists through save/load (verified in the Round 1 round-trip) |

## 5. PDF export

| Requirement | Implemented? | How tested |
|---|---|---|
| Export using browser-native printing / Print to PDF | Yes | `window.print()`; Export PDF button or Ctrl/Cmd+P → Save as PDF |
| Exported PDF contains selectable and copyable text | Yes — as far as verifiable here | `pdftotext` extracted the full text from all three WeasyPrint PDFs (name, contact, summary, every entry, skills, certifications, achievements, positions) — real text, not images. The export path is Print to PDF, which produces real text by design; no canvas/image export is used |
| Maintain A4 dimensions | Yes | `@page { size: A4; margin: 0 }`; WeasyPrint PDFs measure 595.28 × 841.89 pt (A4) |
| Avoid unnecessary blank pages | Yes | 3 clean pages in all templates (an earlier Modern-template bug produced 2 blank pages and was fixed with a print-only floated-sidebar rule) |
| Preserve the selected template | Yes | The print renders the preview as it is, in the selected template |

Still to verify on the user's machine: one real Chrome Export PDF check (page count in print preview + select/copy a line of text).

## 6. Save and load

| Requirement | Implemented? | How tested |
|---|---|---|
| Save resume data as a downloadable JSON file | Yes | **Save as .json** downloads `<name>-resume.json` with every field (including all new sections), the template choice and the section order |
| Import and restore a previously saved JSON file | Yes | **Load .json…** reads it back with FileReader and rebuilds every form field, the template picker, the order list and the preview. Round-trip verified in jsdom (24 assertions): save → clear → load restored template, reordered order, all fields and all new-section entries. Legacy files saved with the old template names (`classic`/`modern`/`minimal`) still load (mapped to the new names; new sections default to empty, no crash) |
| Clear success and error feedback | Yes | Load validates the file and reports errors; the sample JSON in `public/sample-resume.json` was verified to load (jsdom: name, email, template, 9-section order, internship, certification, achievement, position and skills all present in the preview; deep-equal round-trip of the loaded data) |

Work is also autosaved in the browser (localStorage) as you type, so a refresh keeps your edits. Autosave is per-browser; the `.json` file is the portable copy.

## 7. Additional improvements (brief §4)

| Improvement | Implemented? | How tested |
|---|---|---|
| Add and remove education, project and experience entries | Yes | All repeatable lists have add/remove; also internships, certifications, achievements, positions |
| Allow multiple projects and certifications | Yes | Sample preloads 4 projects, 2 certifications |
| Character counter for the professional summary | Yes | Live `n / 600 characters` under the summary box; turns red with a warning past 600. It warns only — it never blocks typing or truncates (a 1500-character summary loaded from JSON keeps its full length). 600 is a recommendation, not a rule from any form |
| Provide sample resume data | Yes | Fake person Riya Sharma preloaded (4 experience, 3 education, 4 projects, 13 skills, 1 internship, 2 certifications, 2 achievements, 1 position); also supplied as `public/sample-resume.json` |
| Add reset and clear options with confirmation | Yes | **Reset to sample** and **Clear all** both ask for confirmation first; cancelling leaves the work untouched (verified in jsdom with `confirm()` returning false/true) |
| Provide a preview mode without editing controls | Yes | **Preview mode** hides the editor and shows only the A4 preview; button becomes **Exit preview**; Esc also exits |
| Support mobile editing | Yes (by CSS inspection) | Under 900px the layout stacks to one column and the A4 sheet becomes `width: 100%`; under 600px form rows and the template picker go single-column; toolbar wraps and is not sticky on mobile. No fixed-width element wider than the viewport remains. Real-phone check still to do on the user's phone |

## UI/UX requirements (brief §4)

| Requirement | Implemented? |
|---|---|
| Clean two-panel layout: editing form left, live A4 preview right | Yes |
| Template selector | Yes — radio picker with the three brief-named templates |
| Section reordering | Yes — drag-and-drop list + ↑/↓ buttons |
| Save/load controls | Yes — Save as .json, Load .json…, autosave |
| PDF export | Yes — Export PDF (Print) button |

## Completion criteria (brief §4)

| Criterion | Status |
|---|---|
| Three visually distinct templates work | ✓ — verified headlessly in preview and in WeasyPrint PDFs |
| A long resume flows correctly across two or more pages | ✓ — 3 A4 pages in all three templates, no stranded headings |
| PDF text can be selected and copied | ✓ (WeasyPrint/`pdftotext` here) / ⏳ final Chrome check outstanding |
| Reordering sections changes the exported resume | ✓ — preview and PDF follow `state.order`; order survives save/load |
| Saved JSON restores all resume information | ✓ — round-trip verified, including legacy files |
| The application works with Wi-Fi disabled | ✓ (zero external references by inspection) / ⏳ final Wi-Fi-off reload on the user's machine outstanding |
