# Automation instructions — Ramp → Puzzle classification

**How to use:** Create one Cursor Automation. Attach this repository. Schedule **daily ~8:00 AM**. Add **both** Ramp MCP and Puzzle MCP via Add Tool or MCP. Paste everything inside the fence below into the automation prompt.

```text
Follow the classify-transactions skill in this repo
(.cursor/skills/classify-transactions/).

Goal
Keep Ramp and Puzzle account classifications in sync.
Classification only — ignore receipts and memos.
Update both sides where MCP allows. Never silent-post journal entries.
Learn: append each high-confidence vendor/type decision to
references/vendor-rules.md.

Setup you already have
- Tools: Ramp MCP + Puzzle MCP (same automation; write enabled as needed)
- Schedule: daily ~8:00 AM (after overnight Ramp → Puzzle sync)
- Skill: classify-transactions

Load first
1. Read references/vendor-rules.md (learned rules — prefer these).
2. Puzzle: list_companies if needed, getChartOfAccounts once; reuse.
3. Ramp: ramp_get_tracking_categories /
   ramp_get_tracking_category_options (for card category writes).
4. Fallback if COA fails: references/chart-of-accounts.md.
   Never invent accounts.

Run steps
1. Read Ramp: card spend AND bill pay. Tag card | bill_pay.
2. Capture account classification (and vendor for bills).
3. Locked Ramp coding → locked_or_skipped; skip Ramp write-back.
4. Match to Puzzle. Compare account classification only.
5. Decide account (vendor-rules.md → evidence → COA allowlist).
6. Puzzle: update matching txn classification via Puzzle MCP
   (categorize/update). Never post/finalize JE; never change amounts/dates.
7. Ramp write-back (unless locked):
   - Card: ramp_edit_transaction + tracking_category_selections
     (accounting category).
   - Bill pay: update account category AND vendor on the Ramp bill.
8. Vendor default: if this vendor should default to that account going
   forward, try Ramp MCP vendor/payee update for default accounting
   category. If no such tool exists, set vendor_default_update=unsupported
   and rely on vendor-rules.md + per-txn writes.
9. Learning loop: append a row to references/vendor-rules.md for each
   high-confidence decision (date | merchant/vendor | account | type |
   reason). Do not wipe existing rows. Persist via repo write/PR when possible.
10. Summary:
    matched | updated | locked_or_skipped | unmatched | errors
    ramp_writeback / puzzle_writeback / vendor_default_update /
    learned_rule_appended counts

If a Puzzle or Ramp write fails, stop further writes of that type and report.
```
