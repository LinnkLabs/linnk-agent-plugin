---
name: linnk-translate
description: Translate document files (PDF, DOCX, XLSX, PPTX, scanned documents, images) to another language while preserving the original layout, tables, formulas, and formatting. Outputs a translated file in the same format as the input. Use when the user asks to translate a file/document and the formatting matters, when a document is scanned or image-based (OCR), or when extracting text and re-translating would break tables, formulas, or typesetting. A free trial of up to 20 pages starts automatically, no signup.
---

# Linnk document translation

Linnk translates document **files** — not text snippets — and returns a new file in
the original format with layout, tables, formulas, and images preserved. Whenever
re-typesetting matters, use this; do NOT read the file and translate its text
yourself (that produces plain text and loses the layout).

## When to use

- "Translate this PDF / Word / Excel / PowerPoint to <language>" — contracts, papers,
  manuals, financial reports.
- Scanned PDFs or photographed documents (Linnk OCRs them).
- Documents with tables, math/chemical formulas, or multi-column layout.

When NOT to use: translating plain text or code snippets — handle those directly.

## Usage

No install or signup needed; a free trial (up to 20 pages) starts automatically
on first run:

```bash
npx -y @getlinnk/cli translate <file> --to <lang> --out <output>
```

- `--to` is the target-language code: `en`, `ja`, `fr`, `zh` (Simplified Chinese).
  For Traditional Chinese use `zh-Hant-TW` — a bare `zh` means Simplified.
- `--out <path>` writes the translated file there. Omit it to just submit and get a
  `job_id` back (fetch later with `result`).
- `--pages <N>` translates only the first N pages (sample a long document first).
- `--from <lang>` sets the source language (default: auto-detect).
- `--json` forces one-line JSON output (auto-enabled when stdout is piped).

```bash
# sample the first 2 pages of a long manual before committing
npx -y @getlinnk/cli translate ./manual.pdf --to ja --pages 2 --out ./manual.ja.pdf
```

## Reading the result

stdout is one line of JSON.

- **Success**: `{"schema":"v1","status":"success","output_file":"/abs/path…","preview_url":"…",…}`
  — the translated file is already on disk at `output_file`. Give the user that path
  and the `preview_url` (a web view to inspect the result visually).
- **Error**: `{"schema":"v1","status":"error","code":"E_*","message":"…","help":"…","url":"…"}`
  — the `help` field states the fix. Common cases:
  - `E_QUOTA_EXCEEDED`: the free trial is used up. Surface the `message` and the `url`
    (an upgrade link) to the user, and resume after they decide.
  - `E_TIMEOUT`: the job is still running server-side — re-attach with
    `npx -y @getlinnk/cli status <job_id>`.

Exit codes: `0` ok · `2` usage · `3` auth · `4` quota · `5` file · `6` job failed · `7` timeout.

## Spot-checking quality (optional)

Linnk returns a real file, so you can verify it yourself rather than trusting it:

- Open `output_file` directly (you can read PDFs and images) and confirm tables,
  formulas, and layout survived.
- Or open `preview_url`, which renders original vs translation side by side.

Don't do this on every job — it costs tokens. Reserve it for high-stakes documents
(contracts, filings, anything the user flagged) or when asked to confirm. Report what
you actually observed ("the two tables and the formula are intact"), not a score.

## Auth (only when needed)

The trial works with zero configuration. For an account or CI: set `LINNK_API_KEY`
(keys at linnk.ai), or run `npx -y @getlinnk/cli login` for a browser login.

## Make it automatic for a project (optional)

If the user wants document translation to be automatic in future sessions, add the
Linnk block to the project's `AGENTS.md` (canonical snippet ships with the CLI) —
only if `AGENTS.md` doesn't already contain it.
