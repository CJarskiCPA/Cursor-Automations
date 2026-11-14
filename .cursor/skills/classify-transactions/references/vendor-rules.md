# Learned vendor / merchant decisions (append-only)

The automation **reads this file at the start of every run** and **appends** new
high-confidence decisions at the end. Do not delete historical rows.

Prefer these mappings when classifying. Account names/codes must exist in Puzzle
`getChartOfAccounts` (or `chart-of-accounts.md` fallback).

| Date (UTC) | Merchant / vendor | Account code | Account name | Type (card\|bill_pay) | Reason |
|------------|-------------------|--------------|--------------|------------------------|--------|
| | | | | | |

<!-- Append new rows below this header table. Keep one decision per row. -->
