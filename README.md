# Cursor Automations

Setup for Cursor Automations used with accounting workflows (Ramp + Puzzle).
GitHub is the source of truth; Notion is a read-mostly catalog refreshed by sync.

## Automations in this repo

| Automation | Trigger | Instructions | Doc |
|------------|---------|--------------|-----|
| Daily Ramp → Puzzle account classification | Daily ~8:00 AM | [`automations/ramp-puzzle-classification.instructions.md`](automations/ramp-puzzle-classification.instructions.md) | [docs/ramp-puzzle-classification.md](docs/ramp-puzzle-classification.md) |
| Sync GitHub repos to Notion (consolidated) | Push to `main` on three repos | [`automations/github-notion-sync.instructions.md`](automations/github-notion-sync.instructions.md) | [docs/github-notion-sync.md](docs/github-notion-sync.md) |

Catalog for Notion sync: [`automations/catalog.json`](automations/catalog.json).

## What the classification automation does

1. Updates Ramp classifications for **card** (`ramp_edit_transaction`) and **bill pay** (category + vendor).
2. Tries to update Ramp **vendor defaults** when MCP allows (otherwise learns in-file).
3. Updates matching **Puzzle** transaction classifications.
4. **Appends** decisions to `.cursor/skills/classify-transactions/references/vendor-rules.md`.

## What the Notion sync does

One automation replaces the old separate Cursor-Automations and coding-agent-skills-library syncs. On push to `main` in any of these repos, it refreshes only that repo’s Notion targets:

- `CJarskiCPA/coding-agent-skills-library` → Coding Agent Skills Library
- `CJarskiCPA/Cursor-Automations` → Cursor Automations catalog
- `CJarskiCPA/ai-employee-toolkit` → shared skills database on AI Employee Toolkit (Department filter)

## Quick start

1. Connect **Ramp** and **Puzzle** cloud MCPs (write enabled as needed) for classification.
2. Connect **Notion** MCP for the sync automation.
3. Create the consolidated sync automation (three push triggers; paste the instructions file).
4. Delete or disable **Sync Cursor Automations to Notion** and **Sync Coding Agent Skills Library to Notion**.

Full steps: [docs/ramp-puzzle-classification.md](docs/ramp-puzzle-classification.md), [docs/github-notion-sync.md](docs/github-notion-sync.md).
