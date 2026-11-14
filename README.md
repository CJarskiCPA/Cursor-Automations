# Cursor Automations

Setup for Cursor Automations used with accounting workflows (Ramp + Puzzle).
GitHub is the source of truth; Notion is a read-mostly catalog refreshed by sync.

## Automations in this repo

| Automation | Trigger | Instructions | Doc |
|------------|---------|--------------|-----|
| Daily Ramp → Puzzle account classification | Daily ~8:00 AM | [`automations/ramp-puzzle-classification.instructions.md`](automations/ramp-puzzle-classification.instructions.md) | [docs/ramp-puzzle-classification.md](docs/ramp-puzzle-classification.md) |
| GitHub → Notion sync | Push to `main` | [`automations/github-notion-sync.instructions.md`](automations/github-notion-sync.instructions.md) | [docs/github-notion-sync.md](docs/github-notion-sync.md) |

Catalog for Notion sync: [`automations/catalog.json`](automations/catalog.json).

## What the classification automation does

1. Updates Ramp classifications for **card** (`ramp_edit_transaction`) and **bill pay** (category + vendor).
2. Tries to update Ramp **vendor defaults** when MCP allows (otherwise learns in-file).
3. Updates matching **Puzzle** transaction classifications.
4. **Appends** decisions to `.cursor/skills/classify-transactions/references/vendor-rules.md`.

## What the Notion sync does

On every push to `main`, refreshes the [Cursor Automations](https://app.notion.com/p/4183fcffe35181eb85b6ee39f4855c55) Notion page, the automations database, and each automation’s detail page from this repo. One-way only (GitHub → Notion).

## Quick start

1. Connect **Ramp** and **Puzzle** cloud MCPs (write enabled as needed) for classification.
2. Connect **Notion** MCP for the sync automation.
3. Create each Cursor Automation (attach this repo; paste the matching instructions file).
4. For Notion sync: trigger = push to `main` on `CJarskiCPA/Cursor-Automations`.

Full steps: [docs/ramp-puzzle-classification.md](docs/ramp-puzzle-classification.md), [docs/github-notion-sync.md](docs/github-notion-sync.md).
