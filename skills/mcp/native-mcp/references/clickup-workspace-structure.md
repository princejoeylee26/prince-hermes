# ClickUp Workspace — Prince/Joey's Setup

Discovered during Honcho + ClickUp MCP setup session, May 2026.

## Workspace

- **Team ID:** `9016545886`
- **URL pattern:** `app.clickup.com/{TEAM_ID}/...`

## Spaces

| Space | ID | Purpose |
|-------|----|---------|
| Personal | `90167053658` | Assets, Habits, Family, Work-A (financial), Hermes Agent, Love, Running, Others |
| Businesses | `90167054047` | Pickpacer Dev, Budgebit Dev, Senta Dev |

## Personal Space Lists

| List | ID | Notes |
|------|----|-------|
| Assets-List | `901615038952` | Utility/mortgage bills |
| Habits-List | `901615038961` | Morning, Lunch, Noon Sleep, Night routines |
| Family-List | `901615038962` | BI-monthly allowance |
| Work-A-List | `901615038966` | Financial tracking (primary) |
| Hermes - Agent Assistant List | `901615048117` | Hermes setup tasks |
| Love-List | `901615038994` | Groceries, government fees |
| Run-List | `901615038972` | Simple list, no custom fields |
| Others-List | `901615038991` | Miscellaneous |

## Businesses Space Lists

| List | ID | Business |
|------|----|---------|
| PP-Dev-List | `901615039715` | Pickpacer |
| BB-Dev-List | `901615039732` | Budgebit |
| SEN-Dev-List | `901615039746` | Senta |

## Custom Fields (Workspace-Level — All Lists)

| Field | Type | Options |
|-------|------|---------|
| 🧲 Action Types | Labels | monthly bills, to pay, to purchase |
| Account | Dropdown | P-Arienne, Cash, BPI, BPI-CC, GCASH |
| Workstream | Dropdown | Rituals, Financial, Development, Marketing, DevOps, Admin |
| Amount Paid | Currency (PHP) | — |
| Income | Currency (PHP) | — |
| Expense | Currency (PHP) | — |
| Budget | Currency (PHP) | — |
| Remaining Balance | Formula (Income − Expense) | auto-compute, no manual pre-fill |

## Financial Rule

`Workstream = "Financial"` is the shared filter for income/expense/budget tracking
across all lists and profiles. Set this on any task that involves money.

## Tags (Workspace-Level)

`january` through `december`, `2026`, `2027`, `2028`, `asset-0001`, `asset-0002`,
`march`, `april`, `may`, `june`, `july`, `august`, `september`

## MCP Server Installed

- **Package:** `@sjotie/clickup-mcp` (npm global install)
- **Version:** v1.8.6
- **Repo:** `github.com/TwoFeetUp/clickup-mcp`
- **CLI:** `clickup-mcp` (on PATH)
- **API Key env var:** `CLICKUP_API_KEY` (format: `pk_...`)
- **Team ID env var:** `CLICKUP_TEAM_ID` (format: numeric string)

## Document Tools

The MCP server has document tools (`manage_document`, `manage_document_page`,
`list_documents`) but they require `DOCUMENT_SUPPORT: "true"` in the server env.
Not enabled yet — enable when ready to create ClickUp Docs.

## Running List

Run-List stays simple — no custom fields, no extra structure.
Training is recorded by the user (Garmin → tell Hermes → create ClickUp task).
Hermes does not generate training plans (coach provides them).