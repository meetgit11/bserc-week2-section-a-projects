# Form Photo Fixer — BSERC Week 2, Section A

Get a photo and signature ready for exam and job application forms in one step, on your own laptop. Single file: open `index.html` in any browser. No uploads, no account, no internet needed.

## How to use

1. **Choose the form** — pick a preset, or add your own custom preset (photo size in px, min/max KB, signature size). Custom presets are saved in the browser (localStorage) and are still there next time you open the page; delete one with its ✕ Delete button.
2. **Photo** — upload a photo (JPEG/JPG/PNG only), or click "Load a sample photo instead" to test with fake data. The original file's name, dimensions and size are shown as soon as it loads. Drag the photo to move it under the frame; the dashed oval is the face guide. Use the Zoom slider and the Crop size slider to resize the crop window (the preset aspect ratio and face guide are kept). Then **Process photo** — a progress line shows each quality step, and the result shows the original crop and the processed image side by side, each labelled with dimensions and KB, plus quality used, size before → after, and KB saved.
3. **Signature** — same flow, for a signature photographed on plain white paper.
4. **Download** — download the finished photo and signature files and check them in your computer's file details (dimensions + KB). Success and error messages appear as small toast notifications (bottom-right) as well as on the page.

Invalid files (anything that is not a .jpg/.jpeg/.png, or an image that cannot be read) are rejected with a clear on-page error and an error toast — the Process button stays disabled so a stale image can never be processed by mistake.

## Presets (checked 5 Nov 2026)

| Preset | Photo | Signature |
|---|---|---|
| JEE Main (NTA) | 350×450 px, JPEG 10–200 KB, 3.5×4.5 cm | 350×150 px, JPEG 4–30 KB, 3.5×1.5 cm |
| Indian Passport — Passport Seva 2.0 | 630×810 px, JPEG 10–250 KB, 35×45 mm | — |
| OCI / Indian e-Visa (square) | 600×600 px, JPEG 10–300 KB, 51×51 mm | — |
| Exam Form A — 50 KB limit | 350×450 px, JPEG 10–50 KB | 350×150 px, JPEG 4–30 KB |

Sources: NTA Information Bulletin (JEE Main); Passport Seva / MEA ICAO spec; OCI / e-Visa portals; the Week 2 PDF preview for Exam Form A. **Form rules change every cycle — always re-check the current official notice for your form; the form's own instructions win if they differ.** The 2027 JEE session reports the signature limit as 10–100 KB (not 4–30 KB), so check your session's bulletin.

## Features

- 4 form presets with documented official sources (JEE Main, Indian Passport, OCI/e-Visa, Exam Form A) + custom presets that persist in localStorage and can be deleted.
- Upload validation (JPEG/JPG/PNG only) with clear on-page errors; original dimensions and file size shown on upload for photo and signature.
- Interactive crop: drag to pan, zoom slider, crop-size slider, face-guide oval, aspect-ratio enforcement.
- Compression to just under the target KB with live progress, then an original-vs-processed comparison (dimensions + KB each), quality used, size before → after and KB saved.
- Toast notifications for uploads, processing, downloads and errors.
- Downloads with meaningful filenames (`photo-<preset>.jpg`, `signature-<preset>.jpg`).

## Limits

- The tool resizes and compresses — it does not retouch, change backgrounds, or guarantee a form will accept the photo. Face position, lighting and background are yours to check against the form's rules.
- If a processed file lands below a form's *minimum* KB, the app warns you (a very plain image compresses too small); a busier photo usually fixes it.
- PNG output skips compression entirely (no quality setting), so it may exceed a KB limit — JPEG is the recommended format. In the comparison view the PNG "original" and "processed" images are the same file, and the app says so.
- "Original" in the comparison view means the same crop at best quality (JPEG quality 100%), before the quality is stepped down — it is the honest before-compression reference, not the untouched uploaded file.
- Tested with the built-in sample photo/signature plus a jsdom test harness (see `test-checklist.md`: 69 + 11 automated assertions). A real-browser end-to-end test on the user's own machine (upload a large phone photo, process, check file details) is still to be done — see `test-checklist.md`.

> **No build step, no package.json:** each app is a single self-contained HTML file. There is no `package.json`, no `node_modules`, and nothing to install — open `index.html` in a browser and it runs.
