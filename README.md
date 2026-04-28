# AFFiNE Exporter

A single-file browser tool that extracts notes from an AFFiNE `.affine` backup and downloads them as Markdown files.

**Live:** https://devin-liu.github.io/affine-exporter/

## How to use

1. Open the AFFiNE desktop app
2. Click your workspace avatar (top-left) → **Settings** → scroll to **Storage & Export** → **Export**
3. Drop the saved `.affine` file onto the tool
4. Click **Extract Notes** and download individual files or a ZIP

## How it works

`.affine` files are SQLite databases. Each document is stored as a [Yjs](https://github.com/yjs/yjs) CRDT binary blob — either as a snapshot, a series of incremental updates, or both. The tool:

- Opens the SQLite file in-browser via [sql.js](https://github.com/sql-js/sql.js) (SQLite compiled to WASM)
- Detects the schema version (`doc_snapshot` for AFFiNE ≥ 0.15, `snapshots`/`updates` for older versions)
- Introspects column names via `PRAGMA table_info` to handle naming variations across versions
- Deserializes each Yjs document and walks the `blocks` map to extract text content
- Converts block flavours (`affine:paragraph`, `affine:list`, `affine:code`, headings) to Markdown
- Packages everything into a ZIP using a hand-rolled ZIP builder (no dependencies)

Everything runs locally in your browser. No data leaves your machine.

## Schema support

| Schema | AFFiNE version | Status |
|---|---|---|
| `doc_snapshot` | ≥ 0.15 | Supported |
| `snapshots` + `updates` | < 0.15 | Supported |
| `workspace` | Legacy | Supported |
| `blobs` | Legacy | Supported |

## Development

It's a single HTML file with no build step.

```sh
# Serve locally
python3 -m http.server
# or
npx serve .
```

## License

MIT — see [LICENSE](LICENSE)
