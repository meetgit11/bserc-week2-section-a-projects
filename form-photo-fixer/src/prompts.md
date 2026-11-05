# prompts.md — Form Photo Fixer (BSERC Week 2, Section A)

Prompts used, in order, as required by the Week 2 PDF.

## 1. Meet → Muse (5 Nov 2026)
> Week 2 PDF + final project list (8 projects, 2 per section). Form Photo Fixer is Section A, Project 2 in the PDF.

Result: PDF read; Form Photo Fixer requirements extracted (3+ presets, crop with face guide, compress to just under KB limit, custom preset, download; Done-when: passes 3 real forms, file details confirm size, large phone photo works with Wi-Fi off).

## 2. Muse → itself (5 Nov 2026, research before building)
> Verify the photo/signature specs for 3 real forms before hard-coding presets — do not invent limits.

Result: web search for Indian Passport (Passport Seva), JEE Main (NTA) and UPSC specs. Findings: JEE Main 2026 photo 10–200 KB / 3.5×4.5 cm and signature 4–30 KB are consistent across sources; Passport Seva 2.0 is 630×810 px, 10–250 KB (the 2×2 in square is OCI/e-Visa, not passport); UPSC specs conflict across sources/years, so UPSC was **not** used as a preset — OCI/e-Visa square was used as the third real form instead, plus the PDF's own 50 KB "Exam Form A" example as a fourth preset. All presets are labelled with their source and a "check the current notice" warning in the app.

## 3. Muse → itself (5 Nov 2026, build)
> Build Form Photo Fixer as one offline single-HTML file: preset selector + custom preset, drag-to-pan crop with face-guide oval and zoom, stepwise JPEG quality reduction to just under the target KB, signature flow, download, ready/not-ready checks, sample photo/signature drawn locally for testing.

Result: `index.html` (26 KB). JS syntax verified with `node --check`; zero external references verified by inspection.

## 4. Meet → Muse (5 Nov 2026, upgrade brief)
> Upgrade Form Photo Fixer in place to the detailed Section A brief — do not rebuild from scratch. Add: original dimensions/size on upload for photo and signature; JPEG/JPG/PNG-only validation with clear on-page errors; resizable crop area (crop-size control); original-vs-processed comparison; compression details + progress; toast notifications; custom presets persisted in localStorage with delete. Keep all presets, face guide, aspect enforcement, just-under-limit compression, signature flow, downloads, offline behaviour. At least 3 build-test-fix rounds.

Result: `index.html` grew 26 KB → 37.6 KB, all 7 features added by editing in place. `node --check` passes after every edit; zero external references; zero `alert()` calls left.

## 5. Test round 1 (5 Nov 2026, jsdom harness `/tmp/fpf-test.js`)
> Run 3 rounds: (1) non-image + PNG + sample uploads, validation and original-size display; (2) resize crop window, process at each preset, check output px/KB and comparison view; (3) add a custom preset, reload, check persistence, delete it.

Result: 66 passed, 3 failed. Failures found and fixed (see prompt 6).

## 6. Test-fix round 2 (5 Nov 2026)
> Fix the 3 Round 1 failures: (a) "KB saved" was negative for JEE — the "original" was a PNG re-encode, which can be smaller than a high-quality JPEG for flat graphics; changed the comparison baseline to the same crop at JPEG quality 100% (size grows with quality, so saved KB is never negative) and relabelled it "Original (cropped, best quality)". (b/c) OCI preset checks read the Passport preset's values — the harness matched preset cards by whole-card text, and the Passport card's source text mentions OCI; fixed the harness to match on the card's title, and made the post-sample wait time-based (the Process button can already be enabled from the previous preset). Also fixed a real app bug found in Round 1: after a failed/corrupt upload the Process button stayed enabled and could have processed a stale image — it is now disabled on any load error.

Result: full harness re-run — **69 passed, 0 failed**.

## 7. Test round 3 — edge cases (5 Nov 2026, jsdom harness `/tmp/fpf-test-round3.js`)
> PNG output format (no quality setting, saved = 0, honest note); invalid custom preset (max ≤ min) rejected with error toast and no card; signature crop-size slider; zoom label; reset restores zoom + crop size; toasts auto-dismiss after 4 s.

Result: **11 passed, 0 failed**. Total across final-state runs: 80 assertions passing.
