# Daily Ramp → Puzzle classification automation

Keeps Ramp and Puzzle **account classifications** in sync, writes coding back to
Ramp (card + bill pay), updates Puzzle, and learns vendor decisions into
`vendor-rules.md`.

## Architecture

```text
vendor-rules.md (learned) + getChartOfAccounts
         ↓
Ramp (card + bill pay)  ←→  Puzzle (matching txns)
         ↓
Writes:
  Puzzle     → update classification on matched txn
  Ramp card  → ramp_edit_transaction (accounting category)
  Ramp bill  → category + vendor
  Ramp vendor default → if MCP supports; else learn in vendor-rules.md
         ↓
Append decision → vendor-rules.md
```

## Behaviors

1. **Ramp card + bill pay coding** — Card: `ramp_edit_transaction` accounting category. Bill pay: category + vendor on the bill.
2. **Vendor default** — When a vendor should inherit that account later, attempt Ramp MCP vendor/payee default-account update. **If MCP has no such tool, skip and record in `vendor-rules.md` only.**
3. **Puzzle sync** — Update classification on the matching Puzzle transaction (categorize/update). Never silent-post journal entries.
4. **Learning loop** — Append each high-confidence vendor/type decision to `.cursor/skills/classify-transactions/references/vendor-rules.md`.

## MCP coverage

| Need | Puzzle MCP | Ramp MCP |
|------|------------|----------|
| COA | `getChartOfAccounts` | Tracking categories / options for coding |
| Card category write | — | **`ramp_edit_transaction`** |
| Bill category + vendor | — | Bill update tools |
| Vendor **default** GL | — | **Not confirmed in MCP tool list** — learn file fallback |
| Txn classification update | Categorize / update txn (write access) | — |
| Rules dump | Not exposed | Not exposed |

## 1. Connect MCPs

1. Connect Ramp + Puzzle; enable write where needed.
2. Add **both** to the automation via Add Tool or MCP.

## 2. Repo layout

```text
.cursor/skills/classify-transactions/
  SKILL.md
  references/
    chart-of-accounts.md   # COA fallback
    vendor-rules.md        # learned decisions (append-only)
automations/
  ramp-puzzle-classification.instructions.md
docs/
  ramp-puzzle-classification.md
```

## 3. Create the automation

| Setting | Value |
|---------|--------|
| Trigger | Daily ~8:00 AM |
| Repository | This repo (needed so `vendor-rules.md` can be updated) |
| Tools | Ramp MCP + Puzzle MCP |
| Instructions | Paste from `automations/ramp-puzzle-classification.instructions.md` |

## 4. Comparison + write logic

1. Load `vendor-rules.md` + Puzzle COA + Ramp tracking categories.
2. Pull Ramp card + bill pay; skip locked for Ramp writes.
3. Match Puzzle; decide account from learned rules + COA.
4. Update Puzzle classification; update Ramp (card category / bill category+vendor).
5. Try vendor default update if MCP allows; else note unsupported.
6. Append learning row to `vendor-rules.md`.

## 5. Constraints

- Classification only (no receipt/memo work)
- Puzzle: update categories; never silent-post JEs
- Append-only learning file — never wipe `vendor-rules.md`
- Both MCPs on one automation
