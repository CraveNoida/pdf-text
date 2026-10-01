# PDF & Image → Text (in-browser OCR)

Everything runs in the browser — no server code, nothing is uploaded anywhere.

    cd pdf_to_text_site
    python3 -m http.server 8000      # then open http://localhost:8000

(Must be served over http — opening index.html by double-click blocks the OCR engine download.)

Files
* index.html                      – the whole app (UI + PDF reading + OCR pipeline)
* tesseract-core-lstm.wasm.js     – Tesseract WASM engine (Apache-2.0)
* Devanagari.traineddata.gz       – Devanagari script model, reads Hindi/Sanskrit + English (Apache-2.0)
* pdf.js is loaded from cdnjs (needs internet); to go fully offline, download pdf.min.js and
  pdf.worker.min.js (v3.11.174) and change the two <script src> lines at the bottom of <body>.

Pipeline (see "image clean-up + accurate OCR pipeline" in index.html)
1. PDF page has a text layer → read directly. Otherwise render at ~1800 px wide; images are scaled to a similar size.
2. Flatten uneven lighting / coloured boxes / shadows, flip dark pages.
3. Page OCR (psm 3) with word-level confidence + boxes.
4. Drop noise: specks, rules, oversized icons, symbol-only tokens, junk lines.
5. Re-read every doubtful line alone at ~100 px height and keep the better reading.
6. Clean-up: stray edge symbols, "।"→"1" numbering fixes, spacing before danda.
