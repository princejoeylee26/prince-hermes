---
name: pr-initial-review
description: "Phase 1 of PR review workflow: review a new PR, find bugs, post findings to GitHub and ClickUp, and code any additional fixes"
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [GitHub, ClickUp, PR-Review, Pull-Requests, Code-Review, Team-Workflow, Phase-1]
    related_skills: [pr-workflow, pr-release-review, github-code-review, clickup-architecture]
---

# PR Initial Review

Phase 1 of the PR review workflow. Invoked when a new PR arrives or when a developer has pushed fixes after our feedback. Reviews code, finds bugs or scope violations, and posts structured feedback to GitHub and ClickUp.

## When to Use

- A new PR arrives from a team developer
- The developer pushed a revised version after our initial review
- Any situation where changes need to be requested before approval

## Inputs Required

Pass these in the task prompt when invoking this skill:
- **PR number** — e.g., `2`
- **Developer name** — e.g., `Randulf`
- **GitHub repo** — e.g., `princejoeylee26/budgebit`
- **ClickUp task ID** — e.g., `86d33g1v0`
- **Branch name** — e.g., `task/86d33g1v0`
- **Review focus areas** — what the ticket scoped, any specific things to verify

## Step-by-Step Procedure

### Step 1: Setup — Get GitHub Token

Extract the token from the active profile's `.env` file using regex. The token variable name in `.env` is `GITHUB_TOKEN`, not `GH_TOKEN`.

```python
import re
import os

hermes_home = os.environ.get("HERMES_HOME", os.path.expanduser("~/.hermes"))

# Determine active profile from HERMES_HOME or use budgebit as default
possible_paths = [
    f"{hermes_home}/profiles/budgebit/.env",
    f"{hermes_home}/profiles/pickpacer/.env",
    f"{hermes_home}/profiles/senta/.env",
    f"{hermes_home}/profiles/personal/.env",
]

GH_TOKEN = None
for path in possible_paths:
    if os.path.exists(path):
        with open(path) as f:
            content = f.read()
        match = re.search(r'GITHUB_TOKEN=(.+)', content)
        if match:
            GH_TOKEN = match.group(1).strip()
            break
```

Shell variables to set:
```bash
GH_OWNER="princejoeylee26"
GH_REPO="budgebit"
PR_NUMBER="2"
TASK_ID="86d33g1v0"
BRANCH_NAME="task/86d33g1v0"
DEV_NAME="Randulf"
REPO_PATH="~/budgebit/repo/budgebit"
```

### Step 2: Fetch and Check Out the PR Branch

```bash
cd $REPO_PATH
git fetch origin pull/$PR_NUMBER/head:pr-$PR_NUMBER
git checkout pr-$PR_NUMBER
git log --oneline -5
git status
```

### Step 3: Establish Base Commit

Identify the merge base so you can diff accurately:
```bash
BASE=$(git merge-base origin/main pr-$PR_NUMBER 2>/dev/null || git merge-base origin/master pr-$PR_NUMBER)
echo "Base: $BASE"
```

### Step 4: Run Automated Checks

Always run TypeScript and lint checks first — they tell you what is obviously broken before you start reading code manually.

**TypeScript:**
```bash
npx tsc --noEmit --project apps/mobile-web/tsconfig.json 2>&1 | grep -E "error TS" | head -30
```

**Lint:**
```bash
npx turbo run lint --filter=mobile-web 2>&1 | tail -20
```

Note pre-existing errors (shared-*, @vendor packages) — distinguish them from new errors introduced by the PR.

### Step 5: Read the Diff

```bash
git diff $BASE..HEAD --stat
git diff $BASE..HEAD --name-only
```

Then for each changed file:
```bash
git diff $BASE..HEAD -- path/to/file.tsx
```

For each file, use `read_file` to see full context around the changes — diffs alone can miss issues visible only with surrounding code.

### Step 6: Manual Review Checklist

Apply this checklist systematically:

**For Constants Migration PRs:**
- All hardcoded strings replaced with imported constants from shared-constants package
- No remaining `grep "yyyy-MM"` or `"yyyy-MM-dd"` in affected scopes
- Import statements correct and pointing to shared-constants
- `STORAGE_KEYS.*` used correctly — value access, not string literal

**Correctness:**
- Does the code do what it claims?
- Edge cases handled (empty inputs, nulls)?
- Error paths handled gracefully?

**Security:**
- No hardcoded secrets, credentials, API keys
- Auth token handling not degraded (check any MCP or auth-related changes)

**Code Quality:**
- Clear naming
- No unnecessary complexity or premature abstraction
- No debug/TODO comments left behind

### Step 7: Code Additional Fixes (If Found)

If issues are small, obvious, and within the ticket scope — code them yourself rather than sending the developer back for a trivial fix.

**Examples of proactive fixes:**
- A few hardcoded strings in a file the PR touched but did not fully migrate
- package-lock.json changes that are unintentional and unrelated to the PR's purpose
- A simple typo or import fix

**How to commit to the developer's branch:**
```bash
# Fix the file using patch tool
patch path/to/file.tsx <<'EOF'
--- a/path/to/file.tsx
+++ b/path/to/file.tsx
@@ -10,6 +10,7 @@
-import { SOME_CONSTANT } from "shared-constants";
+import { SOME_CONSTANT, UPDATED_CONSTANT } from "shared-constants";
EOF

# Commit as a SEPARATE commit — NEVER amend developer's commits
git add path/to/file.tsx
git commit -m "fix: [short description] (on top of $DEV_NAME's PR)"

# Force-push to the developer's branch
git push --force-with-lease origin $(git branch --show-current):$BRANCH_NAME
```

Always create a new commit. Be explicit in the commit message that you added this fix on top of their PR.

### Step 8: Draft the Comments

#### GitHub PR Comment (markdown format, no emojis)

Write in flowing prose with clear sections. Example structure:

```markdown
## Code Review Summary

**Verdict: Changes Requested** ([N] issues found)

**PR:** #[number] — [title]
**Author:** @[developer username]
**Files changed:** [N] files (+[additions] -[deletions])

### [CRITICAL]
<!-- Only if security/data/broken-core issues found -->
- **file.ext:line** — [description]. Fix: [suggestion].

### [WARNING]
<!-- Bugs, broken logic, missing scope items -->
- **file.ext:line** — [description].

### [GOOD]
<!-- Call out things done well — positive reinforcement -->
- [aspect done well]

---
*Reviewed by Hermes Agent — first pass, detailed review.*
```

#### ClickUp Comment (plain text, no markdown, no emojis)

Same content as GitHub comment, translated to plain text. Key differences:
- No `#` headers
- No markdown bullet points — use numbered lists instead
- No code block fences
- No backticks for code (write it inline without backticks)
- Human-readable, paragraph style

Example:
```
Code Review Summary

Verdict: Changes Requested ([N] issues found)

PR: #[number] — [title]
Author: @[developer username]
Files changed: [N] files (+[additions] -[deletions])

[CRITICAL]
- file.ext:line — [description]. Fix: [suggestion].

[WARNING]
- file.ext:line — [description].

[GOOD]
- [aspect done well]

Reviewed by Prince — first pass, detailed review.
```

Always credit the developer by name in both comments. Be direct but encouraging — "nice catch on X", "good approach on Y". When Randulf is the developer, use "Randulf" as the credit.

### Step 9: Post Comments

**Post GitHub comment via curl:**
```python
import json
import subprocess

comment_body = """## Code Review Summary

**Verdict: Changes Requested** (2 issues found)

**PR:** #2 — Constants Migration (86d33g1v0)
**Author:** @randulf
**Files changed:** 6 files (+142 -89)

### [CRITICAL]
- **AuthContext.tsx:210** — localStorage key name is wrong. Using the string literal 'STORAGE_KEYS.APP_LOCK_PIN' instead of the constant value. Fix: use STORAGE_KEYS.APP_LOCK_PIN without quotes.

### [WARNING]
- **aiActionExecutor.ts** — auth token moved from headers to URL params. Verify this is intentional and secure for your use case.

### [GOOD]
- Constants migration consistently applied across all 5 files in scope.

---
*Reviewed by Hermes Agent — first pass, detailed review.*
"""

result = subprocess.run(
    ["curl", "-s", "-X", "POST",
     f"https://api.github.com/repos/{GH_OWNER}/{GH_REPO}/issues/{PR_NUMBER}/comments",
     "-H", f"Authorization: Bearer {GH_TOKEN}",
     "-H", "Accept: application/vnd.github+json",
     "-H", "Content-Type: application/json",
     "-d", json.dumps({"body": comment_body})],
    capture_output=True, text=True
)
response = json.loads(result.stdout)
print(response.get("html_url"))
print(response.get("id"))
```

**Post ClickUp comment via MCP:**
```python
from mcp_clickup_task_comments import create as clickup_comment

clickup_comment(
    action="create",
    task_id=TASK_ID,
    comment_text="ClickUp comment in plain text, same content as GitHub but no markdown."
)
```

**Get ClickUp comment ID:**
```python
# From the result of clickup_comment call
print(f"Comment ID: {result['id']}")
# Save this to add more comments to the same task later
```

### Step 10: Update ClickUp Task Status

If critical bugs are found and the ticket was marked Done but should be reopened:
```python
from mcp_clickup_manage_task import task_update

task_update(
    action="update",
    task_id=TASK_ID,
    status="Open"
)
```

## Proactive Fix Checklist

Before posting your review, ALWAYS check these proactively. If found, fix and commit them yourself:

1. **package-lock.json** — If modified but unrelated to the PR's purpose, revert it:
   ```bash
   # Find pre-PR commit
   BASE=$(git merge-base origin/main HEAD)
   git show ${BASE}:package-lock.json > package-lock.json
   git add package-lock.json
   git commit -m "revert: remove unintentional package-lock.json changes"
   git push --force-with-lease origin $(git branch --show-current):$BRANCH_NAME
   ```

2. **Constants in scope** — Search for any hardcoded strings that should have been migrated but were missed:
   ```bash
   git grep '"yyyy-MM' apps/ functions/ -- :*.ts :*.tsx
   git grep '"yyyy-MM-dd' apps/ functions/ -- :*.ts :*.tsx
   ```

3. **Storage key usage** — Check for the common mistake of passing the constant name string instead of its value:
   ```bash
   git grep "'STORAGE_KEYS" apps/ -- :*.ts :*.tsx
   # Should find nothing — STORAGE_KEYS values should be used, not the string 'STORAGE_KEYS.*'
   ```

## Common Patterns Found During Review

### Storage Key Usage Error

Watch for this pattern:
```typescript
// WRONG — string literal passed
localStorage.removeItem('STORAGE_KEYS.APP_LOCK_PIN');

// CORRECT — constant value access without quotes
localStorage.removeItem(STORAGE_KEYS.APP_LOCK_PIN);
```

## Verification Checklist Before Posting

- [ ] TypeScript check ran — note pre-existing errors separately
- [ ] Lint check ran — no new issues introduced
- [ ] All changed files reviewed
- [ ] package-lock.json checked and reverted if unintentional
- [ ] All hardcoded strings checked in affected scope
- [ ] GitHub comment posted (markdown, no emojis, developer credited by name)
- [ ] ClickUp comment posted (plain text, no markdown, no emojis, developer credited by name)
- [ ] If additional fixes coded: committed with `--force-with-lease`, new commit not amend
- [ ] GitHub comment ID saved for post-review reference
