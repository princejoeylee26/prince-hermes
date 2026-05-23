# ClickUp Custom Fields

8 global custom fields shared across every list in the workspace.

## All Fields

| Field | Type | Available Options / Formula |
|-------|------|----------------------------|
| 🧲 Action Types | Labels | `monthly bills`, `to pay`, `to purchase` |
| Account | Dropdown | `P-Arienne`, `Cash`, `BPI`, `BPI-CC`, `GCASH` |
| Workstream | Dropdown | `Rituals`, `Financial`, `Development`, `Marketing`, `DevOps`, `Admin` |
| Amount Paid | Currency (PHP) | — |
| Income | Currency (PHP) | — |
| Expense | Currency (PHP) | — |
| Budget | Currency (PHP) | — |
| Remaining Balance | Formula | `Income − Expense` (auto-computed, read-only) |

## Usage by Field

### Financial Fields (Workstream = Financial)

Tasks with `Workstream = Financial` use these fields:

- **Income** — money coming in
- **Expense** — money going out
- **Amount Paid** — partial payment against an expense
- **Budget** — allocated budget for a category
- **Remaining Balance** — auto-calculated (Income minus Expense)

**Account field** tracks which account the transaction affects: `P-Arienne`, `Cash`, `BPI`, `BPI-CC`, `GCASH`.

**Action Types** categorizes the action: `monthly bills` (recurring), `to pay` (outstanding), `to purchase` (planned spend).

### Non-Financial Fields

`Workstream` values beyond `Financial`:
- `Rituals` — daily/weekly habits and routines
- `Development` — coding, feature work
- `Marketing` — campaigns, content
- `DevOps` — infrastructure, deployment
- `Admin` — business operations

## Quick Reference for Task Creation

When creating a financial task, set these fields:
```
Workstream: Financial
Income OR Expense: <amount>
Account: <account>
Action Types: <type>
```

When creating a development task:
```
Workstream: Development
Priority: 1-4
Status: <status>
```