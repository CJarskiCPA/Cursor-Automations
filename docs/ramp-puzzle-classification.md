# Daily Ramp → Puzzle classification automation

Concise setup for one Cursor Automation that compares Ramp account classifications to Puzzle (books of record), drafts Puzzle fixes, and writes coding back to Ramp.

## Architecture

```text
Ramp MCP  →  card + bill pay (read classifications)
Puzzle MCP →  getChartOfAccounts + matching transactions (books of record)
         ↓
Compare → Puzzle draft/flag
         ↓
Ramp write-back:
  card     → ramp_edit_transaction (accounting category)
  bill_pay → category + vendor on Ramp bill
```

## MCP coverage (what we use)

| Need | Puzzle MCP | Ramp MCP |
|------|------------|----------|
| Chart of accounts (GL) | **`getChartOfAccounts`** (primary) | Coding fields via `ramp_get_tracking_categories` / options |
| Card category write-back | — | **`ramp_edit_transaction`** (`tracking_category_selections`) |
| Bill pay write-back | — | Bill update tools: **account category + vendor** |
| Configured categorization **rules** | **Not exposed** (Puzzle UI) | **Not exposed** as a rules list |
| Transactions to compare | Transactions / ledger tools | `ramp_get_transactions`, bill tools |

Fetch Puzzle COA and Ramp tracking categories **once per run** and reuse.

## 1. Connect MCPs

1. Connect **Ramp** and **Puzzle** as cloud MCP servers in Cursor.
2. Authenticate both (Ramp needs write scope for `ramp_edit_transaction` / bill edits).
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
| Card → Ramp | **`ramp_edit_transaction`** accounting category |
| Bill pay → Ramp | Account category **and** vendor |
| Puzzle writes | Draft/flag only — never silent-post |
| Locked Ramp items | Skip write-back; do not fail the run |

## 4. Create the automation

At [cursor.com/automations](https://cursor.com/automations):

| Setting | Value |
|---------|--------|
| Trigger | Daily ~8:00 AM (after overnight Ramp→Puzzle sync) |
| Repository | This repo |
| Tools | **Ramp MCP** + **Puzzle MCP** (+ Slack optional) |
| Instructions | Paste from `automations/ramp-puzzle-classification.instructions.md` |

## 5. Comparison + write-back logic

1. Load Puzzle COA + Ramp tracking categories.
2. Pull Ramp card spend **and** bill pay.
3. Locked → `locked_or_skipped` (no Ramp write).
4. Match to Puzzle; compare **account classification only**.
5. Differ/missing → Puzzle flag or draft (account from live COA).
6. **Card:** `ramp_edit_transaction` → accounting category.
7. **Bill pay:** update Ramp **category + vendor**.
8. Never post/finalize in Puzzle.

## 6. Constraints

- Puzzle = books of record (draft/flag only)
- Ramp write-back **is** supported for card category via MCP (`ramp_edit_transaction`)
- Bill pay also writes vendor + category to Ramp
- Both Ramp and Puzzle MCPs on the **same** automation
- Puzzle/Ramp **rules catalogs** remain UI-only
