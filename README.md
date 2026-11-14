# Cursor Automations

Setup for Cursor Automations used with accounting workflows (Ramp + Puzzle).

## Automations in this repo

| Automation | Skill | Doc |
|------------|-------|-----|
| Daily Ramp → Puzzle account classification | `classify-transactions` | [docs/ramp-puzzle-classification.md](docs/ramp-puzzle-classification.md) |

## Quick start

1. Fill `references/chart-of-accounts.md` and `references/vendor-rules.md`.
2. Connect **Ramp** and **Puzzle** cloud MCPs in Cursor.
3. Create a Cursor Automation (daily ~8am) attached to this repo.
4. Add **both** MCPs via Add Tool or MCP.
5. Paste instructions from `automations/ramp-puzzle-classification.instructions.md`.

Full steps: [docs/ramp-puzzle-classification.md](docs/ramp-puzzle-classification.md).
