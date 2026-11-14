# Daily Ramp → Puzzle classification automation

Concise setup for a Cursor Automation that compares Ramp account classifications to Puzzle (books of record).

## Architecture

```text
Ramp (card + bill pay)  →  read classifications
         ↓ match
Puzzle (books)          →  compare; flag/draft fixes only on Puzzle
```

## 1. Connect MCPs (dashboard)

1. Connect **Ramp** and **Puzzle** as cloud MCP servers in Cursor.
2. Authenticate both (OAuth / dashboard Integrations).
3. For Automations: MCPs must be added on the automation itself — account/team MCPs do not carry over automatically.

## 2. Repo layout

```text
.cursor/skills/classify-transactions/
  SKILL.md
  references/
    chart-of-accounts.md
    vendor-rules.md
automations/
  ramp-puzzle-classification.instructions.md
docs/
  ramp-puzzle-classification.md
```

## 3. Skill

- Skill name: `classify-transactions`
- Scope: **account classification only** (no receipt/memo checks)
- Locked Ramp items: skip/report — do not fail the run
- Corrections: **Puzzle side only** (flag or draft). Never write back to Ramp. Never silent-post.

## 4. Create the automation

At [cursor.com/automations](https://cursor.com/automations):

| Setting | Value |
|---------|--------|
| Trigger | Daily ~8:00 AM (after overnight Ramp→Puzzle sync) |
| Repository | This repo |
| Tools | **Ramp MCP** + **Puzzle MCP** (+ Slack summary optional) |
| Instructions | Name the skill and paste from `automations/ramp-puzzle-classification.instructions.md` |

One automation can use **both** MCP servers: use **Add Tool or MCP** twice.

## 5. Automation instructions

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

## 6. Comparison logic

1. Pull Ramp card + bill-pay transactions (recent window / since last run).
2. Note each Ramp account classification and whether it is locked.
3. Locked → `locked_or_skipped`; unlocked → continue.
4. Match to Puzzle (Ramp sync id / merchant / date / amount as available).
5. Compare **account classification only**.
6. Same → `matched`. Different or missing → **Puzzle** flag or draft only.
7. No Ramp writes. No posting/finalizing in Puzzle.

## Constraints

- Puzzle = books of record
- Categorization does not push from Puzzle back to Ramp
- Repo skill named in automation instructions
- Both accounting MCPs on one automation
