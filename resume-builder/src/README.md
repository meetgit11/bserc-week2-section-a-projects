# Resume Builder — BSERC Week 2, Section A, Project 3

A single self-contained HTML file that builds a clean A4 resume from a form,
with a live preview. No account, no upload, no internet needed — everything
stays on your device.

## What it does

1. **Form with a live A4 preview** — type in the left-hand form and the
   A4 preview on the right updates as you type. Sections: personal
   details, summary, experience, internships, education, skills,
   projects, certifications, achievements, positions of responsibility.
   Experience, internships, education, projects, certifications,
   achievements and positions are repeatable entries (add/remove).
2. **3 templates that look truly different** — the names are exactly the
   ones in the detailed brief:

   | Template | Look |
   |---|---|
   | **Modern professional** | Dark header, two-column layout with a sidebar (skills in the sidebar; the other sections follow your dragged order in the main column). |
   | **Minimal ATS-friendly** | Plain single column, no sidebar, no graphics, no tables, left-aligned, simple headings — deliberately plain so applicant tracking systems can parse it. |
   | **Contemporary technical** | Large bold name, extra-bold uppercase headings, wide spacing, no sidebar. |

   Mapping note: the first build used the working names Classic /
   Modern / Minimal. "Modern" became **Modern professional** unchanged;
   "Minimal" (bold headings) became **Contemporary technical**; the old
   "Classic" (serif, centred) was restyled into **Minimal ATS-friendly**
   — serif centred text is not ATS-friendly, so it is now plain
   sans-serif and left-aligned. Old `.json` files saved with the old
   names still load: classic → Minimal ATS-friendly, modern → Modern
   professional, minimal → Contemporary technical.
3. **Drag sections into a new order** — in the "Section order" list, drag
   any of the 9 sections by the ⋮⋮ handle, or use the ↑ / ↓ buttons
   (useful on a phone, where dragging is awkward). The preview and the
   exported PDF both follow the chosen order.
4. **Summary character counter** — under the Professional summary box,
   a live count shows `n / 600 characters`. 600 is the recommended
   length; past it the counter turns red with a "shorten it" warning.
   It never blocks typing or truncates text.
5. **Reset and Clear, both with confirmation** — **Reset to sample**
   asks first, then restores the sample resume (Riya Sharma, including
   the new sections). **Clear all** asks first, then empties every
   field. Cancelling the confirmation leaves your work untouched.
6. **Preview mode** — click **Preview mode** to hide the editor and see
   only the A4 preview; the button becomes **Exit preview** (Esc also
   exits). Printing is unaffected.
7. **Export as PDF** — click **Export PDF (Print)**, or use the browser's
   Print (Ctrl/Cmd+P), and choose **Save as PDF**. The export uses the
   browser's Print to PDF, so the text in the PDF stays selectable and
   copyable. Print CSS is A4 (`@page { size: A4; margin: 0 }`), hides
   the editor/toolbar, and keeps headings with their content
   (`break-after: avoid`, `break-inside: avoid` on sections and entries).
8. **Save your data as a file and load it back** — **Save as .json**
   downloads a file (e.g. `riya-sharma-resume.json`) containing every
   field (including all new sections), the template choice and the
   section order. **Load .json…** reads it back and restores everything.
   Work is also autosaved in the browser (localStorage) as you type, so
   a refresh keeps your edits.
9. **Mobile editing** — on narrow screens the two panels stack (editor
   above the preview), the A4 sheet shrinks to the screen width, rows
   and the template picker go single-column, and there is no horizontal
   overflow. Verified by CSS inspection (see `test-checklist.md`);
   confirm on a real phone before submitting.

## How to open and test

1. Double-click `index.html`, or open it in any modern browser
   (Chrome / Edge / Firefox).
2. It loads with sample data for a fake person, **Riya Sharma**
   (`riya@example.com`) — the sample from the Week 2 PDF preview.
   The sample now includes 1 internship, 2 certifications,
   2 achievements and 1 position of responsibility, so every section
   is visibly working. Replace it with your own details before using
   the resume for real.
3. Try each template, drag the sections into a different order, type a
   long summary and watch the counter, toggle Preview mode, then
   **Save as .json**, change something, and **Load .json…** to check
   the restore. Try **Reset to sample** and **Clear all** — both ask
   for confirmation first.
4. Click **Export PDF (Print)** and check the print preview: the full
   sample resume (9 sections) fills 3 A4 pages in every template, with
   no heading stranded at the bottom of a page. Save the PDF and check
   you can select and copy text from it.
5. To test offline: turn Wi-Fi off and reload the page — it still works,
   because the file has no external fonts, images or scripts (system
   font stacks only).

## Limits — read before submitting

- **Browser print was not directly verified in this build session:**
  no browser (Chrome/Firefox) is installed in the build VM, so the PDF
  was verified by rendering the JavaScript-built page with WeasyPrint
  (see `test-checklist.md`): A4, 3 pages in all three templates, text
  extractable with `pdftotext`. Please do one real **Export PDF** check
  in Chrome before submitting, and confirm the page count in Chrome's
  print preview.
- The on-screen "Preview ≈ N A4 pages" label is an estimate from the
  preview height; the print preview's page count is the authoritative one.
- In the Modern professional template, Skills always sits in the sidebar
  by design; the other eight sections follow the dragged order.
- The PDF export prints the preview as it is — if you want a one-page
  resume, shorten the content; the builder does not shrink text to fit.
- The 600-character summary figure is a recommendation, not a rule
  from any form — the counter warns, it does not enforce.
- Autosave is per-browser (localStorage). The `.json` file is the
  portable copy — keep that, not the browser state, for submission.
- Sample data only. Never paste real documents into a test build;
  the PDF rules say test with fake/sample data, and this file does.

> **No build step, no package.json:** this app is a single self-contained HTML file. There is no `package.json`, no `node_modules`, and nothing to install — open `index.html` in a browser and it runs.
