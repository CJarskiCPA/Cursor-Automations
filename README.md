# Cursor Automations

Setup for Cursor Automations used with accounting workflows (Ramp + Puzzle).

## Automations in this repo

| Automation | Skill | Doc |
|------------|-------|-----|
| Daily Ramp → Puzzle account classification | `classify-transactions` | [docs/ramp-puzzle-classification.md](docs/ramp-puzzle-classification.md) |

## Quick start

1. Connect **Ramp** and **Puzzle** cloud MCPs in Cursor (COA via Puzzle `getChartOfAccounts`; card category write-back via Ramp `ramp_edit_transaction`).
2. Create a Cursor Automation (daily ~8am) attached to this repo.
3. Add **both** MCPs via Add Tool or MCP (Ramp needs write for edits).
4. Paste instructions from `automations/ramp-puzzle-classification.instructions.md`.
5. Optional: fill `.cursor/skills/classify-transactions/references/` only as MCP fallbacks / overrides.

Full steps: [docs/ramp-puzzle-classification.md](docs/ramp-puzzle-classification.md).
