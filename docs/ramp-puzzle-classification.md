# Daily Ramp → Puzzle classification automation

Concise setup for one Cursor Automation that compares Ramp account classifications to Puzzle (books of record).

## Architecture

```text
Ramp MCP  →  card + bill pay classifications (+ optional coding fields)
Puzzle MCP →  getChartOfAccounts + matching transactions (books of record)
         ↓
Compare accounts → flag/draft on Puzzle only
```

## MCP coverage (what we use)

| Need | Puzzle MCP | Ramp MCP |
|------|------------|----------|
| Chart of accounts (GL) | **`getChartOfAccounts`** (primary) | Not a full GL COA on hosted MCP. Optional: `ramp_get_tracking_categories` / options; `ramp_merchant_categories` (spend categories, not GL) |
| Configured categorization **rules** | **Not exposed** (rules live in Puzzle UI) | **Not exposed** as a rules list |
| Transactions to compare | Transactions / ledger tools | `ramp_get_transactions`, bill tools |

Fetch Puzzle COA **once per run** and reuse (Puzzle: reference data does not change within a session).

## 1. Connect MCPs

1. Connect **Ramp** (`https://mcp.ramp.com/mcp` or your Cursor dashboard Ramp connector) and **Puzzle** (`https://app.puzzle.io/mcp`).
2. Authenticate both.
3. On the automation, add **both** via Add Tool or MCP.

## 2. Repo layout

```text
.cursor/skills/classify-transactions/
  SKILL.md
  references/
    chart-of-accounts.md   # fallback only if MCP COA fails
    vendor-rules.md        # optional human overrides
automations/
  ramp-puzzle-classification.instructions.md
docs/
  ramp-puzzle-classification.md
```

## 3. Skill (MCP-first)

| Item | Value |
|------|--------|
| Name | `classify-transactions` |
| COA source | Puzzle **`getChartOfAccounts`** at runtime |
| Rules source | Per-txn classifications from Ramp/Puzzle — **not** a rules dump |
| Fallback | Short static files under `references/` if MCP COA fails |
| Corrections | Puzzle flag/draft only — never Ramp, never silent-post |

You do **not** need to maintain a full static COA for day-to-day runs when Puzzle MCP works.

## 4. Create the automation

At [cursor.com/automations](https://cursor.com/automations):

| Setting | Value |
|---------|--------|
| Trigger | Daily ~8:00 AM (after overnight Ramp→Puzzle sync) |
| Repository | This repo |
| Tools | **Ramp MCP** + **Puzzle MCP** (+ Slack optional) |
| Instructions | Paste from `automations/ramp-puzzle-classification.instructions.md` |

## 5. Comparison logic

1. Load Puzzle COA via `getChartOfAccounts`.
2. Pull Ramp card spend **and** bill pay.
3. Capture each Ramp account classification; locked → `locked_or_skipped`.
4. Match to Puzzle (books of record).
5. Compare **account classification only**.
6. Differ/missing → Puzzle flag or draft only (account from live COA).
7. No Ramp writes. No posting/finalizing in Puzzle.

## 6. Constraints

- Puzzle = books of record
- Categorization does not push from Puzzle back to Ramp
- Instructions must name the `classify-transactions` skill
- Both Ramp and Puzzle MCPs on the **same** automation
- Puzzle/Ramp **rules catalogs** are UI-only until MCP adds them
