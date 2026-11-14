# GitHub → Notion sync automation

Keeps the **Cursor Automations** Notion catalog in sync with this GitHub repo.
One-way only: **GitHub → Notion**. Edits in Notion are overwritten on the next sync.

Modeled on **Sync Coding Agent Skills Library to Notion** for
`CJarskiCPA/coding-agent-skills-library`.

## How the skills-library sync works (pattern)

| Piece | Behavior |
|-------|----------|
| Trigger | GitHub **push to `main`** (not a schedule) |
| Tools | Notion MCP only |
| Direction | GitHub is source of truth; never edit/commit/PR the repo |
| Updates | Upsert Notion DB rows + replace detail page content when version changes |
| Removals | Mark **Retired** — never delete rows |

## What this automation syncs

| Notion target | URL |
|---------------|-----|
| Parent catalog page | https://app.notion.com/p/4183fcffe35181eb85b6ee39f4855c55 |
| Automations database | https://app.notion.com/p/4183fcffe3518168956de474daadf35e |
| Ramp → Puzzle detail (example row) | https://app.notion.com/p/4183fcffe3518130bb01d4558d5a74c2 |

Source of truth for rows: [`automations/catalog.json`](../automations/catalog.json).
Each entry points at instructions + docs files in this repo.

## Create the automation

| Setting | Value |
|---------|--------|
| Name | Sync Cursor Automations to Notion |
| Trigger | GitHub push → repo `CJarskiCPA/Cursor-Automations`, branch `main` |
| Tools | Notion MCP |
| Instructions | Paste from [`automations/github-notion-sync.instructions.md`](../automations/github-notion-sync.instructions.md) |
| Repo write / open PR | Off (one-way sync) |

## After save

1. Confirm the trigger repo is `CJarskiCPA/Cursor-Automations` / `main`.
2. Confirm Notion MCP is connected for the automation.
3. Push a small change to `main` (or re-run) and check Automations → Runs.
4. Verify the catalog page and automation detail pages refreshed.

## Edit workflow

1. Change files in this GitHub repo (`catalog.json`, docs, instructions).
2. Push to `main`.
3. The automation updates Notion. Do not treat Notion as the master copy.
