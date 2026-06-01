---
name: gog-cli
description: Google Workspace CLI (gog) — command reference for Sheets, Docs.
---

# gog — Google Workspace CLI

Fast, script-friendly CLI for Google Workspace. All commands output structured JSON with `--json` or stable TSV with `--plain`.

## Global Flags

```
--account <email>   Account to use (or set GOG_ACCOUNT)
--client <name>     OAuth client (default: "default")
--json              Structured JSON output
--plain             Stable TSV output (no colors)
--force             Skip confirmations
--verbose           Verbose logging
```

## Runtime Notes

- In AlphaClaw-managed deployments, gog state lives under `$OPENCLAW_STATE_DIR` (typically `/data/.openclaw`).
- If a direct shell `gog ...` command falls back to `/root/.config/gogcli` or `/root/.openclaw`, rerun it with `XDG_CONFIG_HOME="${OPENCLAW_STATE_DIR:-$OPENCLAW_HOME/.openclaw}"` so gog uses the managed state dir.
- Always pass `--account <email>` (and `--client <name>` if not "default") so gog targets the correct account.

## Connected Accounts

| Email | Client | Services |
| ----- | ------ | -------- |
| support@chekinfotech.com | default | docs, sheets |

## Sheets

```bash
# Spreadsheet metadata (sheets list, properties)
gog sheets metadata <spreadsheetId>

# Read cell range
gog sheets get <spreadsheetId> 'Sheet1!A1:B10'

# Write cells (pipe-delimited rows, comma-separated columns)
gog sheets update <spreadsheetId> 'A1' 'val1|val2,val3|val4'
gog sheets update <spreadsheetId> 'A1' --values-json '[["a","b"],["c","d"]]'

# Append rows
gog sheets append <spreadsheetId> 'Sheet1!A:C' 'new|row|data'

# Clear range
gog sheets clear <spreadsheetId> 'Sheet1!A1:B10'

# Create spreadsheet
gog sheets create "My Spreadsheet" --sheets "Sheet1,Sheet2"

# Copy spreadsheet
gog sheets copy <spreadsheetId> "Copy Name"

# Export
gog sheets export <spreadsheetId> --format pdf --out ./sheet.pdf
gog sheets export <spreadsheetId> --format xlsx --out ./sheet.xlsx

# Format cells
gog sheets format <spreadsheetId> 'Sheet1!A1:B2' --format-json '{"textFormat":{"bold":true}}' --format-fields 'userEnteredFormat.textFormat.bold'

# Insert rows/columns
gog sheets insert <spreadsheetId> "Sheet1" rows 2 --count 3
gog sheets insert <spreadsheetId> "Sheet1" cols 3 --after

# Cell notes and hyperlinks
gog sheets notes <spreadsheetId> 'Sheet1!A1:B10'
gog sheets links <spreadsheetId> 'Sheet1!A1:B10'
```

Write format: rows separated by `,` and columns by `|`. Use `--values-json` for complex data.
`--copy-validation-from` copies data validation from a reference range when updating/appending.

## Docs

```bash
# Document info
gog docs info <docId>

# Read document text
gog docs cat <docId>
gog docs cat <docId> --max-bytes 10000
gog docs cat <docId> --tab "Notes"
gog docs cat <docId> --all-tabs

# List tabs
gog docs list-tabs <docId>

# Create document
gog docs create "My Doc"
gog docs create "My Doc" --file ./doc.md

# Copy document
gog docs copy <docId> "My Doc Copy"

# Export
gog docs export <docId> --format pdf --out ./doc.pdf
gog docs export <docId> --format docx --out ./doc.docx
gog docs export <docId> --format txt --out ./doc.txt

# Update document content (markdown)
gog docs update <docId> --format markdown --content-file ./doc.md
gog docs write <docId> --replace --markdown --file ./doc.md

# Find and replace
gog docs find-replace <docId> "old text" "new text"

# Sed-style editing (sedmat) with markdown formatting
gog docs sed <docId> 's/hello/**hello**/'          # bold
gog docs sed <docId> 's/hello/*hello*/'             # italic
gog docs sed <docId> 's/hello/`hello`/'             # monospace
gog docs sed <docId> 's/hello/__hello__/'           # underline
gog docs sed <docId> 's/Google/[Google](https://google.com)/'  # link
gog docs sed <docId> 's/{{LOGO}}/![](https://example.com/logo.png)/'  # image

# Tables via sedmat
gog docs sed <docId> 's/{{TABLE}}/|3x4|/'           # create 3-row, 4-col table
gog docs sed <docId> 's/|1|[A1]/**Name**/'          # set cell A1
```
