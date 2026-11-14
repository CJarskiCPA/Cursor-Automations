---
name: classify-transactions
description: >-
  Ramp-first account classification check against Puzzle. Use for daily
  bookkeeping runs that compare Ramp card spend and bill pay classifications
  to matching Puzzle transactions and flag or draft Puzzle-side corrections only.
---

# classify-transactions

Ramp is the source of spend coding. Puzzle is the books of record. This skill
compares **account classification only** (no receipt or memo checks).

## Rules

1. Read **Ramp first**: card spend **and** Ramp bill pay.
2. Capture each Ramp item’s account classification.
3. If Ramp classification is **locked** / not editable: do not error — put it in
   `locked_or_skipped` and continue.
4. Match each item to the corresponding transaction in **Puzzle**.
5. Compare **account classification only**. Ignore receipts and memos.
6. If they match: mark `matched`.
7. If they differ, or Puzzle is missing/wrong: **flag for review** or
   **draft a correction on the Puzzle side only**. Never silent-post or finalize.
8. **Never write classifications back to Ramp.** Categorization does not push
   from Puzzle to Ramp.
9. Use only accounts listed in `references/chart-of-accounts.md` when suggesting
   a Puzzle correction. Prefer `references/vendor-rules.md` for merchant patterns.
10. Never invent accounts, vendors, or amounts. Never change amounts or dates.

## Output for each item

- ramp_id
- puzzle_id (if found)
- ramp_account
- puzzle_account
- locked: yes | no
- status: matched | puzzle_draft_or_flagged | locked_or_skipped | unmatched | error
- reason (1 short sentence)
- action: none | flag_review | draft_puzzle_correction

## Write-back

- Puzzle only, and only as **draft / flag** — never post or finalize.
- Prefer draft/flag when confidence is high and status is `puzzle_draft_or_flagged`.
- If a Puzzle write fails, stop further writes and report the error.
