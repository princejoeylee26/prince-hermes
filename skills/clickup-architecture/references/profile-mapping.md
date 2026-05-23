# Profile Mapping

Complete mapping from profile → ClickUp lists → Honcho memory peer.

## Profile Overview

| Profile | Identity | ClickUp Space | Scope |
|---------|----------|---------------|-------|
| `personal` | Elite runner, married, daily habits | Personal | Run-List, Habits, Family, Love, Assets, Hermes, Work-A, Others |
| `pickpacer` | Running app for Filipino runners | Businesses / Pickpacer | PP-Dev, PP-Marketing, PP-DevOps |
| `budgebit` | Budget tracking app | Businesses / Budgebit | BB-Dev, BB-DevOps, BB-Marketing |
| `senta` | Sentiment analysis app | Businesses / Senta | SEN-Dev, SEN-DevOps, SEN-Marketing |

## Full Mapping Table

### Personal

| Layer | Value |
|-------|-------|
| **ClickUp Space** | Personal (`90167053658`) |
| **ClickUp Lists** | Run-List, Habits-List, Family-List, Love-List, Assets-List, Hermes-Agent-List, Work-A-List, Work-AI-List, List, P-Others-List |
| **Honcho host block** | `hermes.personal` |
| **AI peer** | `hermes.personal` |
| **User peer** | `prince-personal` |
| **Peer identity** | Elite marathon runner, Garmin Instinct 3 AMOLED, married, Morning/Lunch/Noon Sleep/Night routines, personal life only |
| **ClickUp List IDs** | `901615039008`, `901615038961`, `901615038962`, `901615038994`, `901615038952`, `901615048117`, `901615038966`, `901615038972`, `901615048108`, `901615039930` |

### Pickpacer

| Layer | Value |
|-------|-------|
| **ClickUp Space** | Businesses / Pickpacer (`90167054047` / `90169717550`) |
| **ClickUp Lists** | PP-Dev-List, PP-Marketing-List, PP-DevOps-List |
| **Honcho host block** | `hermes.pickpacer` |
| **AI peer** | `hermes.pickpacer` |
| **User peer** | `prince-pickpacer` |
| **Peer identity** | Pickpacer — running app for Filipino runners. Python/FastAPI, Claude Code for dev |
| **ClickUp List IDs** | `901615039715`, `901615039726`, `901615039729` |

### Budgebit

| Layer | Value |
|-------|-------|
| **ClickUp Space** | Businesses / Budgebit (`90167054047` / `90169717557`) |
| **ClickUp Lists** | BB-Dev-List, BB-DevOps-List, BB-Marketing-List |
| **Honcho host block** | `hermes.budgebit` |
| **AI peer** | `hermes.budgebit` |
| **User peer** | `prince-budgebit` |
| **Peer identity** | Budgebit — budget tracking app. Python/FastAPI, Claude Code for dev |
| **ClickUp List IDs** | `901615039732`, `901615039737`, `901615039739` |

### Senta

| Layer | Value |
|-------|-------|
| **ClickUp Space** | Businesses / Senta (`90167054047` / `90169717566`) |
| **ClickUp Lists** | SEN-Dev-List, SEN-DevOps-List, SEN-Marketing-List |
| **Honcho host block** | `hermes.senta` |
| **AI peer** | `hermes.senta` |
| **User peer** | `prince-senta` |
| **Peer identity** | Senta — sentiment analysis app. Python/FastAPI, Claude Code for dev |
| **ClickUp List IDs** | `901615039746`, `901615039752`, `901615039758` |

## Shared Elements (Across All Profiles)

These are NOT isolated per profile — they apply to everything:

| Element | Detail |
|---------|--------|
| **Financial rules** | Shared across all profiles. Tasks with `Workstream = Financial` in any list use Income/Expense/Budget fields |
| **ClickUp custom fields** | All 8 fields are global — shared across Personal and Businesses spaces |
| **ClickUp tags** | Month/year/asset tags apply workspace-wide |
| **ClickUp Team ID** | `9016545886` |
| **Honcho workspace** | Single `hermes` workspace — memory isolated per AI peer, not per workspace |

## How Profile Switching Works

Hermes resolves the active host block from:
1. Current working directory (per-directory session strategy)
2. Explicit profile flag if provided

When you switch context (e.g., from personal to Pickpacer work):
- Hermes loads `hermes.pickpacer` host block from `honcho.json`
- AI peer becomes `hermes.pickpacer`
- User peer becomes `prince-pickpacer`
- Memory context shifts to Pickpacer-only pool
- ClickUp queries target Businesses/Pickpacer lists