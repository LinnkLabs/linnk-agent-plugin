# Linnk for AI agents: translate documents with the layout intact

Your agent can read a PDF, but it cannot hand back a translated PDF that still looks like the original.
Linnk does that part. Give it a PDF, Word, Excel, PowerPoint or scanned file and a target language, and
it returns a new file in the same format with the layout, tables, formulas and images where they were.
The document's text never passes through the agent's conversation: Linnk returns a file and a preview link.

This repo is the plugin and skill for Claude Code, Codex, Cursor and any agent that reads skills. The
command the skill runs is the [`@getlinnk/cli`](https://www.npmjs.com/package/@getlinnk/cli) package on npm.

## Install

Claude Code:

```
claude plugin marketplace add LinnkLabs/linnk-agent-plugin
claude plugin install linnk@linnk
```

Any agent that reads skills (Codex, Cursor, …):

```
npx skills add LinnkLabs/linnk-agent-plugin
```

Or call the CLI directly, no install:

```
npx -y @getlinnk/cli translate ./report.pdf --to ja --out ./report.ja.pdf
```

Then ask in plain words: *"Translate ./contract.pdf into English and keep the layout."*

## What the agent gets back

One line of JSON on stdout.

```json
{"schema":"v1","status":"success","output_file":"/abs/path/report.ja.pdf","preview_url":"https://linnk.ai/preview/...","pages":12,"credits_charged":12,"quota":{"credits_remaining":28}}
```

On failure, `{"schema":"v1","status":"error","code":"E_*","message":"…","help":"…","url":"…"}`; the
`help` field says what to do. Exit codes: `0` ok, `2` usage, `3` auth, `4` quota, `5` file, `6` job
failed, `7` timeout.

## Formats, languages, scans

- PDF (digital and scanned), DOCX, XLSX, PPTX and images. Scans are recognised automatically.
- Target language with `--to` (`en`, `ja`, `fr`, `zh` for Simplified Chinese, `zh-Hant-TW` for
  Traditional). Source language is detected unless you pass `--from`.
- `--pages N` translates only the first N pages, to sample a long document first.

## Trial, accounts, pricing

- The first run starts a free trial of up to 20 pages. No account and no API key.
- After that, `npx -y @getlinnk/cli connect` signs in and opens checkout, or set `LINNK_API_KEY` for CI.
- Agent plans: $10, $20 or $40 a month for 750, 1,500 or 3,000 credits; 20% off yearly. A digital page
  is 1 credit and a scanned page is 4. Details: https://linnk.ai/cli/pricing/

## Privacy

Files are encrypted in transit and at rest and every file is deleted automatically within 72 hours.
Linnk returns a file; it does not put your document's text into the agent's conversation.
Policy: https://linnk.ai/privacy-policy/

## MCP

There is also an MCP server, `@getlinnk/mcp-server` (tools `linnk_translate`, `linnk_get_status`,
`linnk_get_result`, `linnk_upload`). It needs an API key from your Linnk account:

```
claude mcp add linnk -e LINNK_API_KEY=YOUR_KEY -- npx -y @getlinnk/mcp-server
```

## Layout of this repo

`.claude-plugin/marketplace.json` names the marketplace (`linnk`) and its plugin (`linnk`, source
`./claude`); `claude/.claude-plugin/plugin.json` is the plugin; `claude/skills/linnk-translate/SKILL.md`
is the skill, which `npx skills add` picks up as it is.

Product page: https://linnk.ai/cli/ · Plain-text summary for agents: https://linnk.ai/llms.txt ·
MIT licensed · Support: https://linnk.ai/support/
