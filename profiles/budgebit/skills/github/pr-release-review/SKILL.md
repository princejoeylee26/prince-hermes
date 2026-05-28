---
name: pr-release-review
description: "Phase 2 of PR review workflow: verify developer's fixes, code any remaining work, post final approval with appreciation"
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [GitHub, ClickUp, PR-Review, Pull-Requests, Code-Review, Team-Workflow, Phase-2]
    related_skills: [pr-workflow, pr-initial-review, github-code-review, clickup-architecture]
---

# PR Release Review

Phase 2 of the PR review workflow. Invoked after Phase 1 is complete and the developer has pushed fixes. Performs final verification, cleans up any remaining issues proactively, and posts a final approval comment with genuine team appreciation.

## When to Use

- Phase 1 is complete (initial review posted, developer notified)
- Developer pushed new commits addressing your feedback
- This is the final pass before approval or merge

## Inputs Required

Pass these in the task prompt:
- **PR number** — e.g., `2`
- **Developer name** — e.g., `Randulf`
- **GitHub repo** — e.g., `princejoeylee26/budgebit`
- **ClickUp task ID** — e.g., `86d33g1v0`
- **Branch name** — e.g., `task/86d33g1v0`
- **What was found in Phase 1** — list of bugs/issues to verify are fixed

## Step-by-Step Procedure

### Step 1: Setup — Get GitHub Token

Extract the token from the active profile's `.env` file using regex. The token variable name in `.env` is `GITHUB_TOKEN`, not `GH_TOKEN`.

```python
import re
import os

hermes_home = os.environ.get("HERMES_HOME", os.path.expanduser("~/.hermes"))

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

### Step 2: Pull Latest from Developer's Branch

```bash
cd $REPO_PATH
git fetch origin $BRANCH_NAME
git checkout randulf-task-86d33g1v0  # or local branch tracking the PR
git pull origin $BRANCH_NAME --ff-only

# Check what commits are new since our review
git log --oneline origin/main..HEAD
git log --oneline -5
git status
```

### Step 3: Establish Base and Verify Fixes

Compare against the commit hash from your Phase 1 review:
```bash
# Find where Phase 1 ended — use the last commit hash from Phase 1
PHASE1_COMMIT="abc123"  # fill in from Phase 1 output — the last commit on the PR before we started
git diff $PHASE1_COMMIT..HEAD --stat

# Check each file that had issues
git diff $PHASE1_COMMIT..HEAD -- apps/mobile-web/src/contexts/AuthContext.tsx
```

Verify each original bug is fixed. For example, if Phase 1 found the `STORAGE_KEYS.APP_LOCK_PIN` usage error:
```bash
# Was it fixed?
git show HEAD:apps/mobile-web/src/contexts/AuthContext.tsx | grep -n "removeItem"
# Should show: removeItem(STORAGE_KEYS.APP_LOCK_PIN) — no quotes, access the value
```

### Step 4: Run Full Automated Checks

Run the full checks — not just what was previously failing:

**TypeScript:**
```bash
npx tsc --noEmit --project apps/mobile-web/tsconfig.json 2>&1 | grep -E "error TS" | head -30
```

**Lint:**
```bash
npx turbo run lint --filter=mobile-web 2>&1 | tail -20
```

### Step 5: Final Scope Audit

For each file the PR touched, verify the scope is complete:
```bash
# List all changed files in this PR
git diff origin/main..HEAD --name-only > /tmp/changed_files.txt
cat /tmp/changed_files.txt

# Search for any remaining hardcoded strings in changed files
git grep '"yyyy-MM' --all | head -20
git grep '"yyyy-MM-dd' --all | head -20
git grep "'app_lock_pin'" apps/ -- :*.ts :*.tsx

# Check package-lock.json — always check this proactively
git diff origin/main..HEAD --stat | grep package-lock
```

### Step 6: Proactively Code Remaining Fixes

**package-lock.json** — Revert if modified and unintentional:
```bash
# Find the earliest PR commit (before the dev's work started)
git log --oneline origin/main..HEAD  # identify earliest PR commit

# Restore to pre-PR state
PRE_BASE_COMMIT="xyz789"  # get from git log output above
git show ${PRE_BASE_COMMIT}~1:package-lock.json > package-lock.json
git add package-lock.json
git commit -m "revert: remove unintentional package-lock.json changes from PR scope"
git push --force-with-lease origin $(git branch --show-current):$BRANCH_NAME
```

**Other scope violations** — If you find remaining hardcoded strings in files that were supposed to be migrated:
```bash
# Check which files still have hardcoded strings
git grep '"yyyy-MM-dd' apps/mobile-web/src -- :*.ts :*.tsx

# Fix the file directly — use the patch tool
git add <fixed-file>
git commit -m "fix: replace remaining hardcoded yyyy-MM-dd with DATE_FORMATS.DAILY in TransactionDrawer.tsx"
git push --force-with-lease origin $(git branch --show-current):$BRANCH_NAME
```

Do not send the developer back for these. Fix them yourself, commit, push, and note them in the final comment.

### Step 7: Draft Final Comments

Write the final comment in flowing prose. Structure it as:

1. Brief opening — acknowledge the overall PR quality
2. What was reviewed and verified working
3. What we additionally fixed (if any) — commit hash and short description
4. Full commit list on the branch
5. Genuine appreciation — credit the developer by name, call out specific good things
6. Clear final statement — approved, ready to merge

#### GitHub Final Comment (markdown, no emojis)

Example:
```markdown
## Final Review — PR Ready

All fixes verified and looking clean. Here is the complete picture:

**What was reviewed and confirmed:**
1. Constants migration across all 5 files — date formats and storage keys correctly importing from shared-constants
2. AuthContext logout function — STORAGE_KEYS.APP_LOCK_PIN now used correctly without quotes
3. aiActionExecutor transport — auth token handling updated to headers approach
4. mcp.ts — dead commented code removed

**What was additionally fixed on this branch:**
- TransactionDrawer.tsx: 6 hardcoded yyyy-MM-dd strings found and replaced with DATE_FORMATS.DAILY (commit 9fd616f)
- package-lock.json: reverted unintentional lockfile changes (commit 17e2eef)

**Final commit list on this branch:**
1. 86d33g1v0: Complete Constants Migration
2. added changes (AuthContext fix, aiActionExecutor cleanup, mcp.ts dead code removal)
3. fix: TransactionDrawer hardcoded date formats
4. revert: package-lock.json

Randulf, really solid work on this ticket. The migration is thorough, the bonus cleanups were a nice touch, and the fixes all check out. Appreciate you handling it — exactly the kind of systematic, well-scoped work we want. Thanks for the effort.
```

#### ClickUp Final Comment (plain text, no markdown, no emojis)

Same content as the GitHub comment, translated to plain text. Use numbered lists (no `-` bullets). No markdown syntax. No emojis. No backtick code blocks. Write it so someone reading on a phone can follow it easily.

Example:
```
Final Review — PR Ready

All fixes verified and looking clean. Here is the complete picture:

What was reviewed and confirmed:
1. Constants migration across all 5 files — date formats and storage keys correctly importing from shared-constants
2. AuthContext logout function — STORAGE_KEYS.APP_LOCK_PIN now used correctly without quotes
3. aiActionExecutor transport — auth token handling updated to headers approach
4. mcp.ts — dead commented code removed

What was additionally fixed on this branch:
- TransactionDrawer.tsx: 6 hardcoded yyyy-MM-dd strings found and replaced with DATE_FORMATS.DAILY (commit 9fd616f)
- package-lock.json: reverted unintentional lockfile changes (commit 17e2eef)

Final commit list on this branch:
1. 86d33g1v0: Complete Constants Migration
2. added changes (AuthContext fix, aiActionExecutor cleanup, mcp.ts dead code removal)
3. fix: TransactionDrawer hardcoded date formats
4. revert: package-lock.json

Randulf, really solid work on this ticket. The migration is thorough, the bonus cleanups were a nice touch, and the fixes all check out. Appreciate you handling it — exactly the kind of systematic, well-scoped work we want. Thanks for the effort.

Reviewed by Prince.
```

### Step 8: Post Comments

**Post GitHub comment:**
```python
import json
import subprocess

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
print(response.get="html_url")
```

**Post ClickUp comment:**
```python
from mcp_clickup_task_comments import create as clickup_comment

result = clickup_comment(
    action="create",
    task_id=TASK_ID,
    comment_text="Final review plain text — same content as GitHub comment, no markdown."
)
print(f"Comment ID: {result['id']}")
```

### Step 9: Update ClickUp Task Status

```python
from mcp_clickup_manage_task import task_update

task_update(
    action="update",
    task_id=TASK_ID,
    status="Done"
)
```

## Release Review Checklist

Before posting final comments, confirm all of the following:
- [ ] All Phase 1 bugs verified as fixed in the latest commits
- [ ] TypeScript check = no new errors (note pre-existing separately)
- [ ] Lint check = no new errors
- [ ] package-lock.json reverted if it was modified and unintentional
- [ ] Any remaining hardcoded strings in PR scope patched and committed
- [ ] All additional commits force-pushed to developer's branch
- [ ] GitHub final comment posted (markdown, no emojis, genuine appreciation)
- [ ] ClickUp final comment posted (plain text, same content, no markdown)
- [ ] ClickUp task status updated to Done
- [ ] Developer credited by name in both comments

## Force-Push Rule

- Use `git push --force-with-lease` — never plain `--force`
- Always create a new commit for our own fixes — never amend the developer's commits
- Include "(on top of [DEV_NAME]'s PR)" in the commit message when adding our fixes

## Developer Appreciation Guide

When writing the final release comment, make it genuine:
- Call out specific things they did well by name
- Reference specific commits, files, or patterns you observed
- Be concrete ("the migration is thorough", "TransactionDrawer fix was outside scope but much appreciated")
- Use their name — always credit the person, not just "the developer"
- End with a clear statement: PR is approved, ready to merge
