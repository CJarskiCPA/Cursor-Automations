---
name: classify-transactions
description: >-
  Ramp-first classification sync with Puzzle. Updates Ramp card categories
  (ramp_edit_transaction) and bill pay category+vendor; updates Puzzle
  classifications; appends learned vendor decisions to vendor-rules.md.
---

# classify-transactions

Ramp is the source of spend coding. Puzzle is the books of record. This skill
compares and syncs **account classification only** (no receipt or memo checks).

## Startup (every run)

1. **Learned rules** — Read `references/vendor-rules.md` first (accumulated
   prior decisions). Prefer these over inventing new mappings.
2. **Puzzle company** — `list_companies` if needed; use the target `companyId`.
3. **Puzzle chart of accounts** — **`getChartOfAccounts`** once; reuse. Allowlist
   for any account you set on Puzzle or Ramp.
4. **Ramp coding fields** — once per run:
   - `ramp_get_tracking_categories` / `ramp_get_tracking_category_options`
   - optional: `ramp_merchant_categories`
5. **Optional** — Puzzle `getCategorizationMetrics` for backlog context only.

### Fallback

If `getChartOfAccounts` fails, use `references/chart-of-accounts.md`. If that is
empty too, compare/flag only — **never invent accounts**.

### MCP gaps

- Puzzle/Ramp **rules catalogs** are not exposed as list tools.
- **Vendor default GL on Ramp:** no reliable dedicated MCP tool found to set a
  vendor’s default account for future txns. If a vendor-update tool that sets
  default accounting category appears, use it; otherwise record the desired
  default in `vendor-rules.md` and set coding on the current bill/txn only.

## Rules

1. Read Ramp first: **card spend** and **bill pay**. Tag `card` | `bill_pay`.
2. Capture Ramp account classification (and vendor for bill pay).
3. If Ramp coding is **locked**: `locked_or_skipped` — no error, no Ramp write.
4. Match to Puzzle. Compare **account classification only**.
5. Apply decision (from `vendor-rules.md`, then Ramp/Puzzle evidence, then COA):
   - **Puzzle:** update the matching transaction’s account classification via
     Puzzle MCP (categorize/update). Keep books in sync with the decided account.
     Never post/finalize journal entries; never change amounts/dates.
   - **Ramp card:** `ramp_edit_transaction` → accounting category
     (`tracking_category_selections`).
   - **Ramp bill pay:** update **account category** and **vendor** on the bill.
6. **Vendor default (when needed):** If this vendor should default to the chosen
   account going forward, attempt a Ramp MCP vendor/payee update that sets the
   default accounting category. If no such tool is available or it fails, skip
   and rely on `vendor-rules.md` + per-txn writes. Never invent a vendor tool.
7. **Learning loop:** After every high-confidence classification decision for a
   vendor or transaction type, **append** a row to
   `references/vendor-rules.md` (do not wipe the file). Include date, merchant
   or vendor, account code/name, type (card|bill_pay), and short reason.
   Commit or PR the file change when the automation can write the repo.
8. Never invent accounts, vendors, or amounts.

## Output for each item

- ramp_id, puzzle_id, type: card | bill_pay
- ramp_account, puzzle_account, vendor
- locked: yes | no
- status: matched | updated | locked_or_skipped | unmatched | error
- ramp_writeback: none | updated | skipped_locked | failed
- puzzle_writeback: none | updated | failed
- vendor_default_update: none | updated | unsupported | failed
- learned_rule_appended: yes | no
- reason (1 short sentence)

## Write-back summary

| Surface | Action |
|---------|--------|
| Puzzle txn | Update classification (sync with decision); never silent-post JE |
| Ramp card | `ramp_edit_transaction` accounting category |
| Ramp bill | Category + vendor |
| Ramp vendor default | Use MCP if available; else learn in `vendor-rules.md` only |
| `vendor-rules.md` | Append every high-confidence decision |

If a write fails, stop further writes of that type and report the error.
