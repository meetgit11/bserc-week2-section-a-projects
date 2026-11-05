# Form Photo Fixer — requirements traceability

Every mandatory feature from the detailed Section A brief (5 Nov 2026), whether it is implemented, and how it was tested. Sources: `form-photo-fixer/src/test-checklist.md` and `form-photo-fixer/src/prompts.md`. "Verified here" means verified in the build environment with the jsdom harness (fake/stubbed canvas + image decoding, sample data only); "to verify on the user's machine" means it needs a real browser/phone and is **not** claimed as passed.

## 1. Photo and signature upload

| Requirement | Implemented? | How tested |
|---|---|---|
| Support JPEG, JPG and PNG | Yes | jsdom harness: PNG + sample uploads accepted; `.txt` and `.gif` files rejected with a clear on-page error + error toast |
| Display uploaded images in a preview | Yes | Preview shown in the crop workspace for photo and signature |
| Show original dimensions and file size | Yes | Both photo and signature show name, px dimensions, KB and type on load (e.g. "1200 × 900 px · 87.9 KB · PNG") |
| Validate unsupported files, useful error messages | Yes | Non-image and corrupt/unreadable images rejected with a clear error; the Process button is disabled after any failed load so a stale image can never be processed by mistake |

## 2. Form presets

| Requirement | Implemented? | How tested |
|---|---|---|
| At least three configurable form presets | Yes — 4 presets | JEE Main, Indian Passport, OCI/e-Visa, Exam Form A |
| Each preset defines required dimensions and maximum file size | Yes | Preset cards show px dimensions and min–max KB; the app enforces them on export |
| Research and reference three real official form requirements | Yes | See `official-form-references.md`. JEE Main (NTA Information Bulletin), Indian Passport (Passport Seva / MEA ICAO spec), OCI/e-Visa (OCI / e-Visa portals) |
| Use official notices or application instructions as evidence | Yes — named in the app and README, with the official portals listed | The app itself warns that rules change every cycle and the current notice wins |
| Do not invent specifications or falsely claim a preset is officially verified | Yes | Presets are labelled with their source and a "check the current notice" warning; the repository states plainly that no preset is officially verified by BSERC or the authorities. UPSC was **not** used as a preset because its specs conflict across sources/years |
| Allow users to create their own custom preset | Yes | Custom preset form (name, photo W/H px, min/max KB, signature W/H/min/max KB); invalid presets (max ≤ min) rejected with an error toast and no card |

## 3. Image cropping

| Requirement | Implemented? | How tested |
|---|---|---|
| Interactive cropping interface | Yes | Drag-to-pan crop workspace |
| Resizable and movable crop area | Yes | Crop-size slider (50–100%) shrinks/grows the crop window; drag moves the image under it; Reset restores zoom 1.00× and crop size 100% |
| Face-position guide for photographs | Yes | Dashed oval face guide inside the crop window, kept inside it at every crop size |
| Aspect-ratio enforcement | Yes | Crop window aspect is locked to the preset (e.g. 350:450); verified by the harness at all 4 presets |
| Preview before downloading | Yes | Processed image is previewed (with dimensions + KB) before any download |

## 4. Image compression

| Requirement | Implemented? | How tested |
|---|---|---|
| Compress images to fit below the selected file-size limit | Yes | JPEG quality steps down from 92% in 4% steps until the blob is at/below the target KB (default 95% of the preset max) |
| Adjust image quality intelligently | Yes | Quality stepping, not a fixed quality; result reports the quality actually used |
| Display the final file size | Yes | Result shows final KB, quality used, size before → after, and KB saved |
| Never silently claim success if the output exceeds the limit | Yes | Result shows a ✓/✗ against the preset min–max range; a below-minimum result also warns |
| Avoid unnecessary image distortion or stretching | Yes | Crop-then-resize to the preset px (no stretching); the comparison view shows the same crop at best quality as the reference |

## 5. Custom preset

| Requirement | Implemented? | How tested |
|---|---|---|
| Enter custom width, height and maximum file size | Yes | Custom preset form, see §2 |
| Save and reuse custom settings | Yes | Custom presets persist in `localStorage` and survive a fresh page load; each has a ✕ Delete button; built-in presets have no delete button |

## 6. Download

| Requirement | Implemented? | How tested |
|---|---|---|
| Download the processed photograph and signature separately | Yes | Separate Download buttons for photo and signature |
| Use meaningful filenames | Yes | `photo-<preset>.jpg`, `signature-<preset>.jpg`; a download shows a success toast with the filename and KB |
| Display a final validation status | Yes | Ready/not-ready status per file (✓ inside the preset min–max range, ✗ with a reason otherwise) plus a checklist section |

## UI/UX requirements (brief §3)

| Requirement | Implemented? |
|---|---|
| Dashboard-style layout | Yes — stepped sections: choose form → photo → signature → ready-to-upload |
| Clear photo and signature upload cards | Yes — separate upload cards with original-info lines |
| Preset selector | Yes — preset cards with spec + source |
| Interactive crop workspace | Yes — see §3 above |
| Compression details and progress | Yes — live progress line ("Compressing… quality 76% — 84.2 KB (target ≤ 190 KB)") ending with "Done — …" |
| Original versus processed image comparison | Yes — side-by-side, each labelled with dimensions and KB; "Original" = the same crop at JPEG quality 100%, stated honestly in the README |
| Download buttons | Yes |
| Success and error notifications | Yes — toast notifications (auto-dismiss after 4 s) plus on-page messages |

## Completion criteria (brief §3)

| Criterion | Status |
|---|---|
| Three real form presets have documented official sources | ✓ — 4 presets, sources in the app, README and `official-form-references.md` |
| Actual output dimensions match the selected preset | ✓ — verified in the harness at all 4 presets (JEE 350×450, Passport 630×810, OCI 600×600, Exam Form A 350×450) |
| Computer file properties confirm the final file size | ⏳ To verify on the user's machine — download and check file properties in a real browser |
| A large phone photograph works with Wi-Fi disabled | ⏳ To verify on the user's machine — harness used sample/stubbed images, not a multi-MB phone photo in a real browser |
| Cropping, compression and downloading work correctly | ✓ (harness) / ⏳ final real-browser confirmation outstanding |
| Custom presets work | ✓ — persist, reload and delete verified in the harness |
| No image is uploaded to a remote server | ✓ — zero external references in `index.html`; no network code |
