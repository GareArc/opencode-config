---
name: otel-branch-sync
description: Sync changes between OTEL feature branches (1.12.1-otel-ee and feat/otel-telemetry-ee) via cherry-pick, then merge into deploy/enterprise branch.
---

## What I do

I help manage the complex git workflow for the OTEL telemetry feature being developed across multiple branches:

1. **Cherry-pick sync** between:
   - `1.12.1-otel-ee` (based on `release/1.12.1`) - **PRIMARY development branch**
   - `feat/otel-telemetry-ee` (based on `main`) - parallel branch for main-branch compatibility

2. **Merge to deploy/enterprise**: After syncing, merge the appropriate branch into `deploy/enterprise`

## When to use me

Use this skill when:
- You need to sync OTEL-related changes between feature branches
- You've made commits on one branch and want to apply them to another
- You want to merge synced changes into the deploy/enterprise branch
- You need to resolve conflicts during cherry-picking

## Branch Structure

- **1.12.1-otel-ee**: Based on `release/1.12.1` - **PRIMARY working branch** (replaces 1.11.4 as main development)
- **feat/otel-telemetry-ee**: Based on `main` - parallel branch for main-branch compatibility
- **deploy/enterprise**: Deployment target - receives merged changes from primary branch

## Workflow Steps

### 1. Pre-Sync Check
- Verify current branch status and uncommitted changes
- Confirm which direction to sync (between 1.12.1-otel-ee and feat/otel-telemetry-ee)
- List recent commits on source branch for cherry-picking

### 2. Cherry-Pick Sync
- Switch to target branch and ensure it's up to date
- Cherry-pick specified commits from source branch
- Handle conflicts interactively if they occur
- Verify successful application of changes

### 3. Merge to deploy/enterprise (After Sync)
- Switch to deploy/enterprise branch and ensure it's up to date
- Merge the appropriate branch (usually `1.12.1-otel-ee`) into deploy/enterprise
- Handle merge conflicts if any
- Verify the merge was successful

## Common Sync Patterns

### Pattern 1: Primary Development (Most Common)
Work on `1.12.1-otel-ee` → cherry-pick to `feat/otel-telemetry-ee` for main compatibility

### Pattern 2: Main Branch Feature
Work on `feat/otel-telemetry-ee` → cherry-pick to `1.12.1-otel-ee` for enterprise release

### Pattern 3: Deploy
`1.12.1-otel-ee` → merge to `deploy/enterprise` for production deployment

## Rules and Best Practices

### MUST DO
- Always check for uncommitted changes before switching branches
- Ask user to specify which commits to cherry-pick (by hash, range, or "last N commits")
- Show commit summary before cherry-picking to confirm selection
- Use `git cherry-pick` (NOT merge) for cross-branch sync between feature branches
- Use `git merge` (NOT cherry-pick) for deploy/enterprise integration
- Verify the deploy/enterprise branch base before merging
- Run tests after sync if requested

### MUST NOT DO
- Never use merge for syncing between feature branches (they have different bases)
- Never force push without explicit user confirmation
- Never proceed with cherry-pick if there are uncommitted changes
- Never auto-resolve conflicts - always show them to the user
- Never cherry-pick into deploy/enterprise (use merge instead)

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
git log feat/otel-telemetry-ee --not 1.12.1-otel-ee --oneline
git log 1.12.1-otel-ee --not feat/otel-telemetry-ee --oneline
```

## Example Interactions

**Sync from 1.12.1 to feat branch:**
```
User: Sync my last 3 commits to feat branch
Agent: 
1. Checking commits on 1.12.1-otel-ee...
   - abc1234 Add OTEL span creation
   - def5678 Fix attribute naming
   - ghi9012 Update tests
2. Switching to feat/otel-telemetry-ee...
3. Cherry-picking commits...
4. ✓ Successfully synced 3 commits
```

**Merge to deploy/enterprise:**
```
User: Merge into deploy/enterprise
Agent:
1. Confirming deploy/enterprise branch base... ✓
2. Switching to deploy/enterprise branch...
3. Merging 1.12.1-otel-ee...
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
