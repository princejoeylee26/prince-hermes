# Honcho Config

Full structure of `~/.hermes/honcho.json` — the Honcho memory configuration.

## File Location

`~/.hermes/honcho.json`

## Top-Level Config

```json
{
  "apiKey": "<honcho-api-key>",
  "workspace": "hermes",
  "peerName": "prince",
  "contextCadence": 2,
  "dialecticCadence": 3,
  "dialecticDepth": 1,
  "dialecticReasoningLevel": "minimal",
  "dialecticMaxChars": 400,
  "contextTokens": 2000,
  "injectionFrequency": "first-turn",
  "saveMessages": true,
  "hosts": { ... }
}
```

## Hosts

### `hermes` (default / legacy)

```json
"hermes": {
  "enabled": true,
  "aiPeer": "hermes",
  "userPeer": "prince",
  "recallMode": "hybrid",
  "sessionStrategy": "per-directory"
}
```

### `hermes.personal`

```json
"hermes.personal": {
  "enabled": true,
  "aiPeer": "hermes.personal",
  "userPeer": "prince-personal",
  "recallMode": "hybrid",
  "sessionStrategy": "per-directory",
  "dialecticDepth": 1,
  "dialecticReasoningLevel": "minimal",
  "dialecticCadence": 3,
  "contextCadence": 2,
  "dialecticMaxChars": 400,
  "contextTokens": 2000,
  "injectionFrequency": "first-turn",
  "saveMessages": true
}
```

### `hermes.pickpacer`

```json
"hermes.pickpacer": {
  "enabled": true,
  "aiPeer": "hermes.pickpacer",
  "userPeer": "prince-pickpacer",
  "recallMode": "hybrid",
  "sessionStrategy": "per-directory",
  "dialecticDepth": 1,
  "dialecticReasoningLevel": "minimal",
  "dialecticCadence": 3,
  "contextCadence": 2,
  "dialecticMaxChars": 400,
  "contextTokens": 2000,
  "injectionFrequency": "first-turn",
  "saveMessages": true
}
```

### `hermes.budgebit`

```json
"hermes.budgebit": {
  "enabled": true,
  "aiPeer": "hermes.budgebit",
  "userPeer": "prince-budgebit",
  "recallMode": "hybrid",
  "sessionStrategy": "per-directory",
  "dialecticDepth": 1,
  "dialecticReasoningLevel": "minimal",
  "dialecticCadence": 3,
  "contextCadence": 2,
  "dialecticMaxChars": 400,
  "contextTokens": 2000,
  "injectionFrequency": "first-turn",
  "saveMessages": true
}
```

### `hermes.senta`

```json
"hermes.senta": {
  "enabled": true,
  "aiPeer": "hermes.senta",
  "userPeer": "prince-senta",
  "recallMode": "hybrid",
  "sessionStrategy": "per-directory",
  "dialecticDepth": 1,
  "dialecticReasoningLevel": "minimal",
  "dialecticCadence": 3,
  "contextCadence": 2,
  "dialecticMaxChars": 400,
  "contextTokens": 2000,
  "injectionFrequency": "first-turn",
  "saveMessages": true
}
```

## Settings Reference

| Setting | Value | Meaning |
|---------|-------|---------|
| `dialecticDepth` | 1 | Single-pass reasoning only (cheapest) |
| `dialecticMaxChars` | 400 | Hard cap on dialectic output size |
| `contextTokens` | 2000 | Max tokens for memory retrieval |
| `contextCadence` | 2 | Refresh base context every 2 turns |
| `dialecticCadence` | 3 | Dialectic fires every 3 turns |
| `dialecticReasoningLevel` | minimal | Fast/cheap model for dialectic |
| `injectionFrequency` | first-turn | Memory only at session start |
| `recallMode` | hybrid | Semantic + keyword search |
| `sessionStrategy` | per-directory | cwd-based session isolation |

## Peer Card Identities

| Peer | Identity |
|------|---------|
| `prince` | Owner of Pickpacer, Budgebit, Senta. Elite marathon runner. Married. Tech: Python/FastAPI, Claude Code. |
| `prince-personal` | Elite marathon runner, Garmin Instinct 3 AMOLED, married, Morning/Lunch/Noon Sleep/Night routines. Personal life only. |
| `prince-pickpacer` | Pickpacer — running app for Filipino runners. Python/FastAPI, Claude Code for dev. Single business focus only. |
| `prince-budgebit` | Budgebit — budget tracking app. Python/FastAPI, Claude Code for dev. Single business focus only. |
| `prince-senta` | Senta — sentiment analysis app. Python/FastAPI, Claude Code for dev. Single business focus only. |

## Notes

- Peer IDs must match `^[a-zA-Z0-9_-]+$` — dots are NOT allowed in Honcho peer IDs
- `hermes-personal` uses hyphens, not dots, to work around this
- All cost settings are intentionally minimal to control token usage