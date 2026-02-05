---
name: otel-branch-sync
description: Sync changes between OTEL feature branches (1.11.4-otel-telemetry-ee and feat/otel-telemetry-ee) via cherry-pick, then merge into deploy/enterprise branch.
---

## What I do

I help manage the complex git workflow for the OTEL telemetry feature being developed across two parallel branches:

1. **Cherry-pick sync** between:
   - `1.11.4-otel-telemetry-ee` (based on `release/e-1.11.4`)
   - `feat/otel-telemetry-ee` (based on `main`)

2. **Merge to deploy/enterprise**: After syncing, merge `1.11.4-otel-telemetry-ee` into the `deploy/enterprise` branch (which is based on 1.11.4)

## When to use me

Use this skill when:
- You need to sync OTEL-related changes between the two feature branches
- You've made commits on one branch and want to apply them to the other
- You want to merge the synced changes into the deploy/enterprise branch
- You need to resolve conflicts during cherry-picking

## Branch Structure

- **1.11.4-otel-telemetry-ee**: Based on `release/e-1.11.4` - this is the primary working branch
- **feat/otel-telemetry-ee**: Based on `main` - parallel branch for main-branch compatibility
- **deploy/enterprise**: Based on 1.11.4 - receives merged changes from `1.11.4-otel-telemetry-ee`

## Workflow Steps

### 1. Pre-Sync Check
- Verify current branch status and uncommitted changes
- Confirm which direction to sync (1.11.4 → feat or feat → 1.11.4)
- List recent commits on source branch for cherry-picking

### 2. Cherry-Pick Sync
- Switch to target branch and ensure it's up to date
- Cherry-pick specified commits from source branch
- Handle conflicts interactively if they occur
- Verify successful application of changes

### 3. Merge to deploy/enterprise (After Sync)
- Switch to deploy/enterprise branch and ensure it's up to date
- Merge `1.11.4-otel-telemetry-ee` into deploy/enterprise
- Handle merge conflicts if any
- Verify the merge was successful

## Rules and Best Practices

### MUST DO
- Always check for uncommitted changes before switching branches
- Ask user to specify which commits to cherry-pick (by hash, range, or "last N commits")
- Show commit summary before cherry-picking to confirm selection
- Use `git cherry-pick` (NOT merge) for cross-branch sync
- Verify the deploy/enterprise branch is based on 1.11.4 before merging
- Run tests after sync if requested

### MUST NOT DO
- Never use merge for syncing between the two feature branches (they diverge significantly)
- Never force push without explicit user confirmation
- Never proceed with cherry-pick if there are uncommitted changes
- Never auto-resolve conflicts - always show them to the user

### Conflict Resolution
- When cherry-pick conflicts occur, show the conflicting files
- Let user resolve conflicts manually or guide them through it
- After resolution, continue with `git cherry-pick --continue`
- If user wants to abort, use `git cherry-pick --abort`

### Common Commands

**List recent commits on current branch:**
```bash
git log --oneline -n 10
```

**Cherry-pick specific commits:**
```bash
git cherry-pick <commit-hash>
git cherry-pick <hash1> <hash2> <hash3>
git cherry-pick <start-hash>^..<end-hash>
```

**Cherry-pick last N commits:**
```bash
# Get the last N commit hashes and cherry-pick them
git log -n N --format="%H" --reverse | xargs git cherry-pick
```

**Check sync status:**
```bash
# See what commits exist on one branch but not the other
git log feat/otel-telemetry-ee --not 1.11.4-otel-telemetry-ee --oneline
git log 1.11.4-otel-telemetry-ee --not feat/otel-telemetry-ee --oneline
```

## Example Interactions

**Sync from 1.11.4 to feat branch:**
```
User: Sync my last 3 commits to feat branch
Agent: 
1. Checking commits on 1.11.4-otel-telemetry-ee...
   - abc1234 Add OTEL span creation
   - def5678 Fix attribute naming
   - ghi9012 Update tests
2. Switching to feat/otel-telemetry-ee...
3. Cherry-picking commits...
4. ✓ Successfully synced 3 commits
```

**Sync to deploy/enterprise:**
```
User: Merge into deploy/enterprise
Agent:
1. Confirming deploy/enterprise branch is based on 1.11.4... ✓
2. Switching to deploy/enterprise branch...
3. Merging 1.11.4-otel-telemetry-ee...
4. ✓ Successfully merged into deploy/enterprise
```

## Integration with git-master

This skill should ALWAYS be used together with the `git-master` skill:

```
delegate_task(
  category="quick",
  load_skills=["git-master", "otel-branch-sync"],
  description="Sync OTEL commits between branches",
  prompt="..."
)
```

The `git-master` skill provides the git expertise, while this skill provides the specific workflow logic for OTEL branch management.
