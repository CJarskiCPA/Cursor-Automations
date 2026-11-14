# Automation instructions — Ramp → Puzzle classification

Paste this into the Cursor Automation prompt. Enable **Ramp MCP** and **Puzzle MCP** on the same automation. Attach this repository. Schedule daily ~8:00 AM.

```text
Follow the classify-transactions skill.

1. Read Ramp first: card spend AND bill pay; capture each account classification.
2. If Ramp classification is locked: skip/report (locked_or_skipped) — do not error.
3. Match each item to Puzzle (books of record).
4. Compare account classification only (ignore receipts/memos).
5. Match → OK. Differ / missing → flag or draft correction on Puzzle only.
6. Never write back to Ramp. Never silent-post or finalize in Puzzle.
7. Summary: matched, puzzle_draft_or_flagged, locked_or_skipped, unmatched, errors.
```
