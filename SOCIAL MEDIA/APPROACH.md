# Approach (200 words)

I built this as a single-page, client-side app with no backend, so the "working
application URL" deliverable could be a static page deployable in minutes via GitHub
Pages, with no API keys or server costs to manage.

**Extraction:** PDFs are parsed with pdf.js, grouping text items by their y-position
so paragraph/line breaks survive instead of collapsing into one string. Images go
through Tesseract.js OCR, with progress reported back to the UI in real time.

**Analysis:** rather than calling a paid LLM, I built a transparent, rule-based
scoring engine (0–100) covering length, hashtag count, CTA presence, opening-hook
strength, readability, emoji usage, and tone — each weighted and shown with its own
sub-score, plus a suggestions list with specific fixes. This keeps the tool free,
deterministic, and explainable, and the scoring function is isolated so it's a
drop-in swap for an LLM call later if richer feedback is wanted.

**UX/error handling:** drag-and-drop or file picker, multi-file support, per-file
status rows (reading/OCR%/done/failed), a scan-line loading animation, and specific
error messages for unsupported types or unreadable scans — the app stays usable even
when one file fails.

**Trade-offs:** OCR quality depends on scan clarity; multi-column PDFs may re-order
text since grouping is position-based.
