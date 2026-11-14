# Daily Ramp → Puzzle classification automation

Concise setup for one Cursor Automation that compares Ramp account classifications to Puzzle (books of record).

## Architecture

```text
Ramp (card + bill pay)  →  read classifications
         ↓ match
Puzzle (books)          →  compare; flag/draft fixes only on Puzzle
```

## 1. Connect MCPs

1. Connect **Ramp** and **Puzzle** as cloud MCP servers in Cursor.
2. Authenticate both.
3. On the automation, add **both** via Add Tool or MCP (account/team MCPs do not carry over automatically).

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

| Item | Value |
|------|--------|
| Name | `classify-transactions` |
| Path | `.cursor/skills/classify-transactions/` |
| Scope | Account classification only (no receipts/memos) |
| Locked Ramp items | Skip/report — do not fail the run |
| Corrections | Puzzle flag/draft only — never Ramp, never silent-post |

Fill `references/chart-of-accounts.md` and `references/vendor-rules.md` before go-live.

## 4. Create the automation

At [cursor.com/automations](https://cursor.com/automations):

| Setting | Value |
|---------|--------|
| Trigger | Daily ~8:00 AM (after overnight Ramp→Puzzle sync) |
| Repository | This repo |
| Tools | **Ramp MCP** + **Puzzle MCP** (+ Slack optional) |
| Instructions | Paste from `automations/ramp-puzzle-classification.instructions.md` |

## 5. Comparison logic

1. Pull Ramp card spend **and** bill pay (recent window / since last run).
2. Capture each account classification; note locked vs editable.
3. Locked → `locked_or_skipped` (no error).
4. Match to Puzzle (books of record).
5. Compare **account classification only**.
6. Same → `matched`. Different/missing → **Puzzle** flag or draft only.
7. No Ramp writes. No posting/finalizing in Puzzle.

## 6. Constraints

- Puzzle = books of record
- Categorization does not push from Puzzle back to Ramp
- Instructions must name the `classify-transactions` skill
- Both Ramp and Puzzle MCPs on the **same** automation
