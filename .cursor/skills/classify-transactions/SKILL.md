---
name: classify-transactions
description: >-
  Ramp-first account classification check against Puzzle. Compares Ramp card
  spend and bill pay to Puzzle; drafts Puzzle fixes; writes account category
  back to Ramp for card spend (ramp_edit_transaction) and category + vendor
  for bill pay.
---

# classify-transactions

Ramp is the source of spend coding. Puzzle is the books of record. This skill
compares **account classification only** (no receipt or memo checks).

## Startup (every run) — load reference data via MCP

Fetch once per run, then reuse (Puzzle docs: reference data does not change within a session).

1. **Puzzle company** — `list_companies` if needed; use the target `companyId`.
2. **Puzzle chart of accounts** — call **`getChartOfAccounts`** (Puzzle MCP).
   Use this as the allowlist of account names/codes/IDs for any suggested
   Puzzle correction. Prefer live MCP data over static files.
3. **Ramp coding fields** (Ramp MCP) — fetch once:
   - `ramp_get_tracking_categories` / `ramp_get_tracking_category_options`
     (needed to set accounting category on card txns via `ramp_edit_transaction`)
   - optional: `ramp_merchant_categories` (spend categories, not GL)
4. **Optional metrics** — Puzzle `getCategorizationMetrics` for backlog context only
   (not a rules list).

### Fallback (only if MCP COA fetch fails)

Use `references/chart-of-accounts.md` and optional overrides in
`references/vendor-rules.md`. If those are empty and MCP failed, **do not invent
accounts** — compare/flag only; skip suggested account names.

### What MCP does **not** expose

- **Puzzle custom / PuzzleBot categorization rules** — managed in Puzzle UI
  (Rules); no MCP “list rules” tool found. Do not assume a rules dump exists.
- **Ramp auto-categorization / coding rules** — not listed as MCP tools.
  Infer only from each transaction’s current Ramp classification fields.

## Rules

1. Read **Ramp first**: card spend **and** Ramp bill pay. Tag each item as
   `card` or `bill_pay`.
2. Capture each Ramp item’s account classification (and vendor for bill pay).
3. If Ramp classification is **locked** / not editable: do not error — put it in
   `locked_or_skipped` and continue. Do not attempt Ramp write-back when locked.
4. Match each item to the corresponding transaction in **Puzzle**.
5. Compare **account classification only**. Ignore receipts and memos.
6. If they match: mark `matched`.
7. If they differ, or Puzzle is missing/wrong: **flag for review** or
   **draft a correction on the Puzzle side**. Never silent-post or finalize
   in Puzzle.
8. **Also write back to Ramp** when you classify/update (unless locked):
   - **Card spend:** Ramp MCP **`ramp_edit_transaction`** — set accounting
     category via `tracking_category_selections` (resolve UUIDs from
     `ramp_get_tracking_categories` / `ramp_get_tracking_category_options`).
     Do **not** invent vendor changes on cards unless the tool requires it.
   - **Bill pay:** Ramp MCP — update **account category** and **vendor** on
     that Ramp bill.
9. When suggesting accounts, use only live `getChartOfAccounts` (or fallback
   file). Prefer `references/vendor-rules.md` only as optional human overrides.
10. Never invent accounts, vendors, or amounts. Never change amounts or dates.

## Output for each item

- ramp_id
- puzzle_id (if found)
- type: card | bill_pay
- ramp_account
- puzzle_account
- vendor (bill_pay)
- locked: yes | no
- status: matched | puzzle_draft_or_flagged | locked_or_skipped | unmatched | error
- ramp_writeback: none | updated | skipped_locked | failed
- reason (1 short sentence)
- action: none | flag_review | draft_puzzle_correction | draft_puzzle_and_ramp

## Write-back

### Puzzle (card + bill pay)
- Draft / flag only — never post or finalize.
- Prefer draft/flag when confidence is high and status is `puzzle_draft_or_flagged`.
- If a Puzzle write fails, stop further Puzzle writes and report the error.

### Ramp
- **Card:** `ramp_edit_transaction` → accounting category (`tracking_category_selections`).
- **Bill pay:** update account category **and** vendor on the Ramp bill.
- If locked or write fails: do not retry blindly; record `ramp_writeback` and stop
  further Ramp writes for the run.
