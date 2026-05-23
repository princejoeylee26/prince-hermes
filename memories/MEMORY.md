# Hermes Memory — Consolidated

## Core Setup

- **MCP server:** @sjotie/clickup-mcp v1.8.6 (clickup-mcp CLI, STDIO mode)
- **GitHub backup repo:** princejoeylee26/prince-hermes (SSH: git@github.com:princejoeylee26/prince-hermes.git)
- **ClickUp Team ID:** 9016545886
- **ClickUp custom fields:** 8 global (Action Types, Account, Workstream, Amount Paid, Income, Expense, Budget, Remaining Balance)
- **Session strategy:** per-directory (cwd-based) — optimal for Termux

## Profile Architecture

| Profile | User Peer | AI Peer | Host Block | ClickUp Lists |
|---------|-----------|---------|-----------|---------------|
| personal | prince-personal | hermes.personal | hermes.personal | Run-List, Habits-List, Family-List, Love-List, Assets-List, Hermes-Agent-List, Work-A-List, P-Others-List |
| pickpacer | prince-pickpacer | hermes.pickpacer | hermes.pickpacer | PP-Dev-List, PP-Marketing-List, PP-DevOps-List |
| budgebit | prince-budgebit | hermes.budgebit | hermes.budgebit | BB-Dev-List, BB-DevOps-List, BB-Marketing-List |
| senta | prince-senta | hermes.senta | hermes.senta | SEN-Dev-List, SEN-DevOps-List, SEN-Marketing-List |

## Honcho Memory Config

- **Workspace:** hermes
- **All peers:** prince, prince-personal, prince-pickpacer, prince-budgebit, prince-senta
- **Peer IDs:** hyphens only, no dots (Honcho constraint: `[a-zA-Z0-9_-]+`)
- **Cost settings:** dialecticDepth=1, contextTokens=2000, dialecticMaxChars=400, contextCadence=2, dialecticCadence=3
- **Full mapping:** `~/.hermes/skills/clickup-architecture/`

## Critical Rules

1. **Timezone:** Asia/Manila (UTC+8) — ClickUp uses ms timestamps, ALWAYS divide by 1000 before datetime.fromtimestamp()
2. **Financial rules:** Shared across all profiles via `Workstream = Financial` in ClickUp
3. **Document rule:** Conditional only — business requirement, actual output, or reports generated
4. **Running:** Hermes records coach-given training + Garmin data. Does NOT generate training plans.
5. **Naming:** Call user "Prince" not "Joey"

## Hermes Profiles

| Profile | Location | Launch command | Telegram switch |
|---------|----------|---------------|-----------------|
| personal | `~/.hermes/` | `hermes` or `~/h` | "switch to personal" |
| pickpacer | `~/.hermes/profiles/pickpacer/` | `pickpacer chat` or `~/pickpacer/hp` | "switch to pickpacer" |
| budgebit | `~/.hermes/profiles/budgebit/` | `budgebit chat` or `~/budgebit/hb` | "switch to budgebit" |
| senta | `~/.hermes/profiles/senta/` | `senta chat` or `~/senta/hs` | "switch to senta" |

Each profile has its own: `config.yaml`, `SOUL.md`, `honcho.json`, `memories/` directory.
ClickUp MCP is configured on all profiles. Telegram profile switching is done mid-conversation — I call Honcho to change the active peer.

## Session Behavior

- `per-directory` strategy: session key derived from cwd
- At `~/.hermes/` → personal profile (hermes.personal host block)
- At project dirs → respective business profile
- Files in `~/.hermes/memories/` (MEMORY.md, USER.md) migrate to Honcho on first session init

## VPS Plan (Deferred)

- Install hermes → clone prince-hermes repo → pip install honcho-ai → set HONCHO_API_KEY → `hermes memory setup`
- Docker-compose: Postgres, Redis, Hermes Agent, Honcho backup schedule
- Hostinger or similar (user deferred setup)