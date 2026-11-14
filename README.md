# Cursor Automations

Setup for Cursor Automations used with accounting workflows (Ramp + Puzzle).

## Automations in this repo

| Automation | Skill | Doc |
|------------|-------|-----|
| Daily Ramp → Puzzle account classification | `classify-transactions` | [docs/ramp-puzzle-classification.md](docs/ramp-puzzle-classification.md) |

## What it does

1. Updates Ramp classifications for **card** (`ramp_edit_transaction`) and **bill pay** (category + vendor).
2. Tries to update Ramp **vendor defaults** when MCP allows (otherwise learns in-file).
3. Updates matching **Puzzle** transaction classifications.
4. **Appends** decisions to `.cursor/skills/classify-transactions/references/vendor-rules.md`.

## Quick start

1. Connect **Ramp** and **Puzzle** cloud MCPs (write enabled as needed).
2. Create a Cursor Automation (daily ~8am) attached to this repo.
3. Add **both** MCPs via Add Tool or MCP.
4. Paste instructions from `automations/ramp-puzzle-classification.instructions.md`.

Full steps: [docs/ramp-puzzle-classification.md](docs/ramp-puzzle-classification.md).
