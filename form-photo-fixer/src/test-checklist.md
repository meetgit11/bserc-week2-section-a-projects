# Test checklist — Form Photo Fixer (BSERC Week 2, Section A)

PDF "Done when": output meets the rules of 3 real forms; link their official notices in the report; your computer's file details confirm the size in KB; a large phone photo works with Wi-Fi off.

Detailed-brief completion criteria: 3 real form presets with documented official sources; actual output dimensions match the selected preset; computer file properties confirm the final file size; a large phone photograph works with Wi-Fi disabled; cropping, compression and downloading work correctly; custom presets work; no image is uploaded to a remote server.

## Verified in this workspace (5 Nov 2026, upgraded version)

Tested with a jsdom harness (`/tmp/fpf-test.js` + `/tmp/fpf-test-round3.js`, fake/stubbed canvas + image decoding, sample data only). Final-state runs: **69/69 + 11/11 assertions passed**.

- [x] **JS syntax** — `node --check` on the extracted script after every edit: passes.
- [x] **Offline** — zero external references in `index.html` (grep for `http(s)://` returns none); system fonts only; zero `alert()` calls left.
- [x] **4 presets, each with size + KB limit** — JEE Main, Indian Passport, OCI/e-Visa, Exam Form A (50 KB). Each preset card shows its spec; sources are listed in the app and in `README.md`.
- [x] **Original dimensions + file size on upload** — photo and signature both show name, px dimensions, KB and type (e.g. a 1200×900 px, 90000-byte PNG shows "1200 × 900 px · 87.9 KB · PNG") before any processing.
- [x] **Upload validation** — a .txt file and a .gif signature are rejected with a clear on-page error + error toast; a corrupt/unreadable image shows "could not be read… try a different .jpg or .png file"; the Process button is disabled after any failed load, so a stale image can never be processed by mistake.
- [x] **Resizable crop area** — crop-size slider (50–100%) shrinks/grows the crop window while keeping the preset aspect ratio and the face guide inside it; returns to full size at 100%; works independently for photo and signature; Reset restores zoom 1.00× and crop size 100%.
- [x] **Compress to just under the limit** — JPEG quality steps down from 92% in 4% steps until the blob is at/below the target KB (default 95% of the preset max); processed at all 4 presets and the output px/KB landed inside each preset's min–max range (JEE 350×450, Passport 630×810, OCI 600×600, Exam Form A 350×450).
- [x] **Original vs processed comparison** — after processing, both images are shown side by side, each labelled with dimensions and KB. "Original" = the same crop at best quality (JPEG 100%), the honest before-compression reference.
- [x] **Compression details + progress** — a progress line shows each quality step live ("Compressing… quality 76% — 84.2 KB (target ≤ 190 KB)") and ends with "Done — …"; the result shows quality used, size before → after, and KB saved (never negative — fixed in test round 2).
- [x] **Toasts** — success toasts for upload/processing/download, error toasts for validation failures; toasts auto-dismiss after 4 s.
- [x] **Custom presets persist** — a custom preset (400×500 px, 10–80 KB) is written to localStorage, is still present in a fresh page load with the persisted data, and is removed from UI + localStorage by its ✕ Delete button. Built-in presets have no delete button. An invalid custom preset (max ≤ min) is rejected with an error toast and no card.
- [x] **Signature flow** — separate upload/crop/process/download with its own original-info, validation, crop-size slider, comparison and progress; sample signature processes to 350×150 px inside 4–30 KB; disabled automatically for presets with no signature spec.
- [x] **PNG output format** — no quality setting; the app says so, and original/processed are the same file (saved 0.0 KB), stated honestly.
- [x] **Sample photo/signature** — drawn locally on canvas (fake data), so the tool can be tested without a real photo.
- [x] **Downloads** — meaningful filenames (`photo-<preset>.jpg`, `signature-<preset>.jpg`); a download shows a success toast with the filename and KB.

## Still to verify on the user's machine (no browser in this VM)

- [ ] Open `index.html` with Wi-Fi off, upload a **large phone photo** (several MB), process it for the JEE preset, and confirm the downloaded file's details show 350×450 px and ≤ 200 KB.
- [ ] Do the same for the Passport preset (630×810 px, ≤ 250 KB) and check the face sits inside the oval in the preview.
- [ ] Confirm the official notices named in the README still match the current cycle before submitting a real form.
- [ ] Confirm the toasts, crop-size slider and comparison view behave the same in the user's real Chrome as in the jsdom harness (the harness stubs canvas decoding; the logic is the same, but real-browser rendering is the final check).
