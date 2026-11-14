# Automation instructions — Ramp → Puzzle classification

**How to use:** Create one Cursor Automation. Attach this repository. Schedule **daily ~8:00 AM**. Add **both** Ramp MCP and Puzzle MCP via Add Tool or MCP. Paste everything inside the fence below into the automation prompt.

```text
Follow the classify-transactions skill in this repo
(.cursor/skills/classify-transactions/).

Goal
Compare Ramp account classifications to Puzzle (books of record).
Account classification only — ignore receipts and memos.
Flag or draft mismatches on Puzzle only. Never silent-post.

Setup you already have
- Tools: Ramp MCP + Puzzle MCP (same automation)
- Schedule: daily ~8:00 AM (after overnight Ramp → Puzzle sync)
- Skill: classify-transactions

Run steps
1. Read Ramp first: card spend AND bill pay.
2. For each item, capture the account classification.
3. If Ramp classification is locked / not editable:
   put it in locked_or_skipped and continue — do not error or fail the run.
4. Find the matching transaction in Puzzle (books of record).
5. Compare account classification only.
6. If they match → matched.
7. If they differ, or Puzzle is missing/wrong →
   flag for review or draft a correction on Puzzle only.
8. Never write classifications back to Ramp
   (Puzzle does not push category changes to Ramp).
9. Never post, finalize, void, or change amounts/dates/payees in Puzzle.
10. End with a short summary:
    matched | puzzle_draft_or_flagged | locked_or_skipped | unmatched | errors

If a Puzzle write fails, stop further writes and report the error.
Use references/chart-of-accounts.md and references/vendor-rules.md
when suggesting a Puzzle correction.
```
