# PR Workflow — Decision Guide

Use this reference to decide which phase to invoke when given a PR review task.

## Decision Tree

```
Received a PR from a team developer?
│
├── First time seeing this PR
│   └── Load: pr-initial-review
│
├── Has developer already pushed fixes after your initial review?
│   ├── YES
│   │   └── Load: pr-release-review
│   └── NO (still waiting for fixes)
│       └── Wait — do not proceed to Phase 2
│
└── Did Phase 1 find bugs but developer has not yet fixed?
    └── Load: pr-initial-review (do not proceed until fixes arrive)
```

## Skill Responsibilities

| Skill | Trigger | Output |
|-------|---------|--------|
| `pr-initial-review` | New PR or revised PR with bugs | Bug report posted, fixes requested, task status updated |
| `pr-release-review` | Dev pushed fixes after Phase 1 | Final approval, proactive cleanup, appreciation posted |
| `pr-workflow` | This reference | Decision logic only |

## Profile Context

These skills are written for the **Budgebit** profile context:
- Repo path: `~/budgebit/repo/budgebit`
- GitHub repo: `princejoeylee26/budgebit`
- ClickUp workspace: Budgebit workspace (list IDs in clickup-architecture skill)
- Default developer: Randulf (other devs may be added)

For other projects (Pickpacer, Senta), copy these skills to the respective profile directory and update `REPO_PATH`, `GH_OWNER`, `GH_REPO` in the skill steps.
