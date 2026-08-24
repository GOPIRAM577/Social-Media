# Social Media Content Analyzer

A browser-based tool that extracts text from an uploaded PDF or scanned image and
scores it for social media engagement, with concrete edit suggestions.

**Live in the browser, no backend, no API key.** Everything — PDF parsing, OCR, and
the scoring engine — runs client-side.

## Features

- **Upload:** drag-and-drop or file picker, accepts PDFs and image files (PNG/JPG/etc.), multiple files at once.
- **PDF parsing:** extracts text with [pdf.js](https://mozilla.github.io/pdf.js/), grouping text items by line position to keep basic paragraph structure instead of one long run-on string.
- **OCR:** scanned images are read with [Tesseract.js](https://tesseract.projectnaptha.com/), with a live progress readout per file.
- **Loading states:** each uploaded file gets a status row (reading → OCR %/parsing → done/failed); a scan-line animation runs over the extracted-text panel while processing.
- **Error handling:** unsupported file types, empty/unreadable scans, and extraction failures each surface a specific message without breaking the rest of the app.
- **Engagement analysis:** a transparent, rule-based scoring engine (0–100) covering:
  - Length (ideal ~40–150 words)
  - Hashtag count (ideal 3–8)
  - Call-to-action presence
  - Opening-line hook strength (first ~120 characters, since most feeds truncate there)
  - Readability (average words per sentence)
  - Emoji usage
  - Tone / ALL-CAPS abuse
  - Plus a suggestions list with specific, actionable fixes.

## Why rule-based instead of an LLM call?

This keeps the tool fully client-side, free, deterministic, and reviewable — no API
key to manage or leak, no rate limits, and every score is explainable line-by-line.
The scoring function (`runAnalysis` in `index.html`) is isolated on purpose: swapping
it for a call to a free-tier LLM (e.g. sending the extracted text and asking for a
score + suggestions as JSON) is a drop-in change if richer, more contextual feedback
is wanted later.

## Run it locally

No build step. Just open the file:

```bash
open index.html   # macOS
# or
xdg-open index.html   # Linux
# or just double-click index.html
```

## Deploy it (for the "working application URL" deliverable)

**Option A — GitHub Pages (recommended, free, ~2 minutes):**

1. Create a new GitHub repo and push this folder:
   ```bash
   git init
   git add .
   git commit -m "Social media content analyzer"
   git branch -M main
   git remote add origin https://github.com/<your-username>/<repo-name>.git
   git push -u origin main
   ```
2. In the repo: **Settings → Pages → Source → Deploy from a branch → `main` / root**.
3. Your live URL will be `https://<your-username>.github.io/<repo-name>/`.

**Option B — Netlify Drop:** go to [app.netlify.com/drop](https://app.netlify.com/drop) and drag this folder in. You get a live URL instantly, no account needed.

## Project structure

```
social-media-content-analyzer/
├── index.html      # entire app: markup, styles, and logic
├── README.md        # this file
└── APPROACH.md       # 200-word write-up of the approach
```

## Tech stack

- Vanilla HTML/CSS/JS (no framework, no build step — chosen so the "working application
  URL" deliverable is a single static page deployable in minutes)
- [pdf.js](https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js) for PDF text extraction
- [Tesseract.js](https://cdnjs.cloudflare.com/ajax/libs/tesseract.js/4.1.1/tesseract.min.js) for OCR

## Known limitations

- OCR accuracy depends on scan quality; heavily skewed or low-res images will extract poorly (Tesseract limitation, not fixable client-side without a preprocessing step).
- PDF line-grouping is position-based, so unusual multi-column layouts may re-order text.
- The "Clear all" / per-file remove buttons reset the combined text panel rather than surgically removing one file's contribution — documented trade-off to keep extraction fast and stateless per file.
