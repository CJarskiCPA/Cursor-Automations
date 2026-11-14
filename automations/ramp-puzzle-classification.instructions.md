# Automation instructions — Ramp → Puzzle classification

**How to use:** Create one Cursor Automation. Attach this repository. Schedule **daily ~8:00 AM**. Add **both** Ramp MCP and Puzzle MCP via Add Tool or MCP. Paste everything inside the fence below into the automation prompt.

```text
Follow the classify-transactions skill in this repo
(.cursor/skills/classify-transactions/).

Goal
Compare Ramp account classifications to Puzzle (books of record).
Account classification only — ignore receipts and memos.
Flag or draft mismatches on Puzzle (never silent-post).
Also write corrections back to Ramp:
- Card spend → accounting category via ramp_edit_transaction
- Bill pay → account category AND vendor on the Ramp bill

Setup you already have
- Tools: Ramp MCP + Puzzle MCP (same automation)
- Schedule: daily ~8:00 AM (after overnight Ramp → Puzzle sync)
- Skill: classify-transactions

Load reference data first (MCP)
1. Puzzle: list_companies if needed, then getChartOfAccounts once; reuse for the run.
2. Ramp: ramp_get_tracking_categories /
   ramp_get_tracking_category_options (needed for card category write-back).
3. Do NOT expect a Puzzle or Ramp “list categorization rules” tool —
   those rules are not exposed over MCP. Use each txn’s current classification.
4. Fallback only if getChartOfAccounts fails:
   references/chart-of-accounts.md (and optional vendor-rules.md).
   If still empty, compare/flag only — never invent accounts.

Run steps
1. Read Ramp first: card spend AND bill pay. Tag each as card or bill_pay.
2. For each item, capture the account classification (and vendor for bills).
3. If Ramp classification is locked / not editable:
   put it in locked_or_skipped and continue — do not error; skip Ramp write-back.
4. Find the matching transaction in Puzzle (books of record).
5. Compare account classification only.
6. If they match → matched.
7. If they differ, or Puzzle is missing/wrong →
   flag for review or draft a correction on Puzzle only
   (suggested account must come from getChartOfAccounts / fallback file).
8. Ramp write-back (unless locked):
   - Card: ramp_edit_transaction with tracking_category_selections
     for the accounting category.
   - Bill pay: update account category AND vendor on the Ramp bill.
9. Never post, finalize, void, or change amounts/dates/payees in Puzzle.
10. End with a short summary:
    matched | puzzle_draft_or_flagged | locked_or_skipped | unmatched | errors
    and ramp_writeback counts (updated / skipped_locked / failed)

If a Puzzle or Ramp write fails, stop further writes of that type and report the error.
```
