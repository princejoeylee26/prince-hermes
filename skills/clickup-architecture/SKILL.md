---
name: clickup-architecture
description: Full architecture for Prince's ClickUp workspace — ClickUp lists, Honcho memory peers, and profile isolation mapping. Use this whenever working with ClickUp tasks or Honcho memory context.
owner: Prince Joey Lee
workspace: hermes
last_updated: 2026-05-23
---

# ClickUp Architecture

Complete reference for the 4-profile setup: ClickUp task structure + Honcho memory isolation per profile.

## Quick Reference

| Profile | ClickUp Space | ClickUp Folders | Honcho host | User peer |
|---------|--------------|-----------------|-------------|-----------|
| **personal** | Personal | Run-List, Habits, Family, Love, Assets, Hermes, Work-A, Others | `hermes.personal` | `prince-personal` |
| **pickpacer** | Businesses / Pickpacer | PP-Dev, PP-Marketing, PP-DevOps | `hermes.pickpacer` | `prince-pickpacer` |
| **budgebit** | Businesses / Budgebit | BB-Dev, BB-DevOps, BB-Marketing | `hermes.budgebit` | `prince-budgebit` |
| **senta** | Businesses / Senta | SEN-Dev, SEN-DevOps, SEN-Marketing | `hermes.senta` | `prince-senta` |

## Profile Scope

**personal** — Elite marathon runner, married, daily habits, personal finances, family, love, assets.
**pickpacer** — Running app for Filipino runners. Development, marketing, DevOps.
**budgebit** — Budget tracking app. Development, marketing, DevOps.
**senta** — Sentiment analysis app. Development, marketing, DevOps.

## ClickUp Custom Fields (global — shared across all lists)

| Field | Type | Options |
|-------|------|---------|
| 🧲 Action Types | Labels | monthly bills, to pay, to purchase |
| Account | Dropdown | P-Arienne, Cash, BPI, BPI-CC, GCASH |
| Workstream | Dropdown | Rituals, Financial, Development, Marketing, DevOps, Admin |
| Amount Paid | Currency (PHP) | — |
| Income | Currency (PHP) | — |
| Expense | Currency (PHP) | — |
| Budget | Currency (PHP) | — |
| Remaining Balance | Formula | `Income − Expense` (auto-computed) |

## ClickUp Timestamp Handling

**⚠️ CRITICAL — Timezone is ALWAYS Manila (Asia/Manila, UTC+8)**

ClickUp returns due dates as millisecond Unix timestamps. Python's `datetime.fromtimestamp()` without timezone assumes local time, which on Termux/Android may not be Manila. Always use explicit timezone:

```python
from datetime import datetime, timezone, timedelta

MANILA = timezone(timedelta(hours=8))

# Correct — divide ms by 1000, pass tz
dt = datetime.fromtimestamp(ts / 1000, tz=MANILA)
```

**Common bug:** `datetime.fromtimestamp(ts / 1000)` without `tz=MANILA` silently produces wrong hour (off by hours if device local ≠ Manila).

**Quick conversion in conversation:**
- ClickUp ms timestamp → Manila datetime: `datetime.fromtimestamp(ts / 1000, tz=MANILA)`
- Natural language for ClickUp due filter: `"today"` or `"tomorrow"` work; for specific dates use `"2026-05-24"`

Month tags: `january`–`december`
Year tags: `2026`, `2027`, `2028`
Asset tags: `asset-0001`, `asset-0002`
Other: `march`, `april`, `may`, `june`, `july`, `august`

---

## Detailed Structure

### Personal Profile

**ClickUp Space:** Personal (`90167053658`)

| Folder | List ID | List Name |
|--------|---------|-----------|
| Running | `901615039008` | Run-List |
| Habits | `901615038961` | Habits-List |
| Family | `901615038962` | Family-List |
| Love | `901615038994` | Love-List |
| Assets | `901615038952` | Assets-List |
| Hermes | `901615048117` | Hermes-Agent-List |
| Work | `901615038966` | Work-A-List |
| Work | `901615038972` | Work-AI-List |
| Others | `901615048108` | List |
| Others | `901615039930` | P-Others-List |

**Running rules:** Hermes records coach-given training + Garmin data. Does not generate training plans. Run-List is a simple list with no extra custom fields. `Remaining Balance` is auto-computed.

**Financial tasks** (Workstream = Financial) exist in Work-A-List. Financial rules are shared across all profiles — not isolated per profile.

---

### Pickpacer Profile

**ClickUp Space:** Businesses / Pickpacer (`90167054047` / `90169717550`)

| List ID | List Name | Workstream tags |
|---------|---------|-----------------|
| `901615039715` | PP-Dev-List | Development |
| `901615039726` | PP-Marketing-List | Marketing |
| `901615039729` | PP-DevOps-List | DevOps |

---

### Budgebit Profile

**ClickUp Space:** Businesses / Budgebit (`90167054047` / `90169717557`)

| List ID | List Name | Workstream tags |
|---------|---------|-----------------|
| `901615039732` | BB-Dev-List | Development |
| `901615039737` | BB-DevOps-List | DevOps |
| `901615039739` | BB-Marketing-List | Marketing |

---

### Senta Profile

**ClickUp Space:** Businesses / Senta (`90167054047` / `90169717566`)

| List ID | List Name | Workstream tags |
|---------|---------|-----------------|
| `901615039746` | SEN-Dev-List | Development |
| `901615039752` | SEN-DevOps-List | DevOps |
| `901615039758` | SEN-Marketing-List | Marketing |

---

## Honcho Memory Architecture

**Workspace:** `hermes` (1 workspace — all 4 profiles share it, but memory is isolated per AI peer)

| AI peer | Writes to user peer | Memory pool |
|---------|---------------------|-------------|
| `hermes` | `prince` | base/legacy (default host) |
| `hermes.personal` | `prince-personal` | personal life only |
| `hermes.pickpacer` | `prince-pickpacer` | Pickpacer only |
| `hermes.budgebit` | `prince-budgebit` | Budgebit only |
| `hermes.senta` | `prince-senta` | Senta only |

**⚠️ Honcho peer ID constraints:**
- Peer IDs must match `^[a-zA-Z0-9_-]+$` — **dots are NOT allowed**
- Use hyphens instead: `prince-personal` NOT `prince.personal`
- The host block key in `honcho.json` CAN use dots (`hermes.personal`); only the peer ID has this restriction

**⚠️ Honcho `set_card()` API (Python SDK):**
```python
from honcho import Honcho
client = Honcho(api_key="...")

peer = client.peer("prince-personal")
peer.set_card(peer_card=[
    "Elite marathon runner training with coach",
    "Garmin Instinct 3 AMOLED for training data",
    "Personal life only — no business context"
])
```
- Takes `peer_card: list[str]` — a list of strings, NOT keyword args
- Each string in the list = one line of the peer card identity
- Peer cards are per-user-peer, not per-host-block

**Session strategy:** `per-directory` — each working directory gets its own Honcho session context. Switch directories = switch memory context.

**Cost settings (all profiles — minimal):**
- `dialecticDepth`: 1
- `dialecticMaxChars`: 400
- `contextTokens`: 2000
- `contextCadence`: 2 (refresh every 2 turns)
- `dialecticCadence`: 3 (fires every 3 turns)
- `dialecticReasoningLevel`: minimal
- `injectionFrequency`: first-turn

---

## Memory Files (USER.md, SOUL.md, MEMORY.md)

Three memory files live in `~/.hermes/memories/` and `~/.hermes/SOUL.md`:

| File | Location | Content | Uploads to |
|------|----------|---------|-----------|
| `USER.md` | `~/.hermes/memories/` | User profile, preferences, identity | `user_peer` (Honcho) |
| `SOUL.md` | `~/.hermes/` | Agent persona and tone | `ai_peer` (Honcho) |
| `MEMORY.md` | `~/.hermes/memories/` | Setup facts, architecture, rules | `user_peer` (Honcho) |

**Migration:** On first session init with `session_strategy ≠ per-session`, `migrate_memory_files()` uploads these to Honcho. With `per-session` strategy they are uploaded each fresh run. With `per-directory` (current) they upload once per directory context.

**Session key resolution:** Derived from cwd path. At `~/.hermes/` → session key `.hermes` → host `hermes` → peer `prince`. At a project dir → respective profile host block.

**When to update:** Whenever user corrects a preference, workflow, or fact. These files are the canonical source of truth for Honcho migration.

## Document Rule

Docs are created **conditionally** — not preemptively:
1. When a specific business requirement exists
2. When actual output is produced for a task
3. When reports are generated

Format: Markdown. Parent page with subpages for multi-page docs.

---

## Profile Switching

To switch between profiles, Hermes uses the `honcho.json` host blocks. The correct host block is resolved from the current working directory or explicit profile flag.

**Current host blocks:**
- `hermes` — default/legacy
- `hermes.personal` — personal life
- `hermes.pickpacer` — Pickpacer
- `hermes.budgebit` — Budgebit
- `hermes.senta` — Senta

---

## Related Skills

- `hermes-agent` — Hermes configuration and setup
- `clickup-mcp-usage` — How to use the ClickUp MCP tools

---

## Files in This Skill

- `references/clickup-ids.md` — All ClickUp list/space/folder IDs
- `references/custom-fields.md` — Custom field definitions
- `references/profile-mapping.md` — Profile ↔ ClickUp ↔ Honcho mapping
- `references/honcho-config.md` — Full honcho.json structure