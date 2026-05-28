---
name: pr-workflow
description: "Decides which PR review phase to run: initial review (bugs found) or release review (dev fixed, final pass)"
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [GitHub, ClickUp, PR-Review, Pull-Requests, Code-Review, Team-Workflow]
    related_skills: [pr-initial-review, pr-release-review, clickup-architecture, github-code-review]
---

# PR Review Workflow Orchestrator

Orchestrates the two-phase PR review process for team pull requests. Determines which phase to invoke based on where you are in the review cycle.

## Decision Tree

```
Received a PR from a team developer?
│
├── First time reviewing this PR / Developer has NOT yet pushed fixes after feedback
│   └── Load: pr-initial-review
│
└── Developer already pushed fixes AFTER your initial review
    └── Load: pr-release-review
```

## Inputs Required (both phases)

When invoking either phase, provide:
- **PR number** — e.g., `2`
- **Developer name** — e.g., `Randulf`
- **GitHub repo** — e.g., `princejoeylee26/budgebit`
- **ClickUp task ID** — e.g., `86d33g1v0`
- **Branch name** — e.g., `task/86d33g1v0`
- **Review focus areas** — what the ticket scoped, any specific concerns to verify

## The Two Phases

### Phase 1: Initial Review (`pr-initial-review`)

Applied when:
- PR is new, never reviewed
- PR was returned with changes requested and dev is now submitting a new version
- Any bugs or issues found that require developer to act

Actions:
1. Check out developer's PR branch locally
2. Run diff review against base branch
3. Run TypeScript/lint checks
4. Identify bugs, issues, missing scope
5. Code any additional fixes that are obvious and small yourself
6. Commit and force-push additional fixes to dev's branch
7. Post GitHub PR comment (markdown, detailed, no emojis)
8. Post ClickUp comment (plain text, same content, developer credited by name)
9. Mark task status if needed

### Phase 2: Release Review (`pr-release-review`)

Applied when:
- Phase 1 is complete (findings posted, fixes requested)
- Developer has pushed new commits addressing your feedback
- This is the final verification before approving

Actions:
1. Pull latest from developer's branch
2. Verify all initial bugs are fixed
3. Run full TypeScript/lint checks
4. Audit for any remaining scope violations
5. Check and revert `package-lock.json` changes if present and unintentional
6. Code any remaining issues found (not dev's fault — clean them up yourself)
7. Commit and force-push all additional fixes
8. Post final GitHub comment: approval, what's done, developer credited by name, genuine appreciation
9. Post final ClickUp comment: same message in plain text
10. Update task status to Done

## Comment Style Rules

| Platform | Style |
|----------|-------|
| **GitHub** | Markdown, numbered lists, code blocks for diffs, no emojis |
| **ClickUp** | Plain text, numbered lists, no markdown, no emojis, human-readable |
| **Both** | Developer credited by name, clear and specific, genuine tone |

## Force-Push Rule

When committing to a developer's branch:
- Use `git push --force-with-lease` — never plain `--force`
- Always create a new commit (do NOT amend developer's commits)
- Explain in the commit message that you are adding to the PR

## Related Skills

- `pr-initial-review` — Phase 1: bugs found, changes requested
- `pr-release-review` — Phase 2: dev fixed, final verification + approval
- `github-code-review` — underlying code review mechanics
- `clickup-architecture` — ClickUp workspace structure
