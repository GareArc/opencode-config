# OTEL Branch Sync Skill

Sync changes between OTEL feature branches and merge into deploy branch.

## Branch Structure

- **Primary Feature Branch**: `1.12.1-otel-ee` (based on `release/1.12.1`)
- **Legacy Feature Branch**: `1.11.4-otel-telemetry-ee` (based on `release/e-1.11.4`)
- **Deploy Branch**: `deploy/enterprise` (production deployment branch)

## Sync Strategy

### 1.12.1 → 1.11.4 Backport (when needed)
When critical fixes are made on `1.12.1-otel-ee` that need to be backported:

```bash
git switch 1.11.4-otel-telemetry-ee
git cherry -v 1.11.4-otel-telemetry-ee 1.12.1-otel-ee
# Cherry-pick only OTEL-specific commits, skip general platform changes
git cherry-pick <commit-hash>
```

### 1.12.1 → deploy/enterprise (production deployment)
When ready to deploy to production:

```bash
git switch deploy/enterprise
git merge --no-ff 1.12.1-otel-ee -m "merge: OTEL telemetry features from 1.12.1-otel-ee"
```

## Commit Selection Rules

**Always cherry-pick**:
- Commits with `feat(telemetry):` or `fix(telemetry):` prefix
- Commits with `feat(enterprise):` related to OTEL
- Commits touching `enterprise/telemetry/` directory
- Commits related to OpenTelemetry, OTLP, tracing, metrics

**Never cherry-pick**:
- General platform refactorings (web UI, API structure)
- Dependency updates unrelated to telemetry
- Feature additions outside telemetry scope
- Database migrations unrelated to telemetry

## Conflict Resolution Strategy

When conflicts occur during cherry-pick:
1. Preserve the **target branch's base compatibility** (e.g., 1.11.4 patterns for legacy branch)
2. Port the **OTEL feature behavior** from the source commit
3. Resolve file-by-file, never use blanket `--ours`/`--theirs`
4. For modify/delete conflicts: keep deletion if file is obsolete, restore if needed by feature

## Verification After Sync

```bash
# Run tests on synced branch
uv run pytest tests/unit_tests/enterprise/telemetry/ -v
uv run ruff check ./
uv run basedpyright .

# Compare branches for OTEL parity
git cherry -v <source-branch> <target-branch>
git range-diff <common-base>..<source-branch> <common-base>..<target-branch>
```

## Notes

- The `1.12.1-otel-ee` branch is the primary development branch going forward
- The `1.11.4-otel-telemetry-ee` branch is maintained for legacy support only
- All new OTEL features should be developed on `1.12.1-otel-ee` first
- Backports to `1.11.4-otel-telemetry-ee` are selective and only for critical fixes
