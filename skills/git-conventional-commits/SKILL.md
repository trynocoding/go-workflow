---
name: git-conventional-commits
description: Go project git conventions — Conventional Commits format with Go-aware scope naming, atomic commit discipline, and breaking change handling. Load when writing commit messages or reviewing commit history.
user-invocable: false
allowed-tools: []
---

# Git Conventional Commits

## Commit Message Format

```
<type>(<scope>): <subject>

[optional body]

[optional footer(s)]
```

### Types

| Type | When to use |
|---|---|
| `feat` | New feature or capability |
| `fix` | Bug fix |
| `refactor` | Code restructure with no behavior change |
| `perf` | Performance improvement |
| `test` | Add or update tests |
| `docs` | Documentation only |
| `chore` | Build, tooling, dependency updates |
| `ci` | CI/CD pipeline changes |
| `revert` | Revert a previous commit |

### Scope (Go-project conventions)

Use the **package name** or **module area** as scope:

```
feat(api): add pagination to list endpoints
fix(controller): handle nil reconcile result
refactor(pkg/util): extract retry helper
perf(store): reduce allocs in hot path
test(integration): add e2e for CRD lifecycle
chore(deps): upgrade controller-runtime to v0.18
```

Common scopes for Go projects:
- `api` — API types, protobuf, REST handlers
- `cmd` — main entry points
- `pkg/<name>` — library packages
- `internal/<name>` — internal packages
- `controller` / `reconciler` — K8s controller logic
- `store` / `cache` — data layer
- `config` — configuration
- `deps` — dependency changes

### Subject Rules

- Lowercase, no period at end
- Imperative mood: "add", "fix", "remove" (not "added", "fixes")
- ≤72 characters total for first line
- Describe **what** changed, not **how**

### Body (when needed)

Add a body when:
- The motivation for the change isn't obvious
- The change is complex and needs context
- Breaking changes need explanation

Separate from subject with a blank line. Wrap at 72 chars.

### Breaking Changes

```
feat(api)!: rename ListOptions.Limit to ListOptions.PageSize

BREAKING CHANGE: ListOptions.Limit has been renamed to PageSize
for consistency with gRPC conventions. Update all call sites.
```

Use `!` after type/scope AND add `BREAKING CHANGE:` footer.

## Atomic Commit Discipline

**One logical change per commit.** Never bundle unrelated changes.

Good:
```
fix(controller): prevent double-reconcile on status update
```

Bad (mixed concerns):
```
fix stuff and add feature and update deps
```

**Stage selectively when needed:**
```bash
git add -p              # interactive hunk staging
git add path/to/file    # specific file
```

## Before Every Commit

1. `git diff --cached` — review exactly what's staged
2. Verify scope matches the changed packages
3. Confirm subject is imperative mood
4. Check: does this commit do ONE thing?

## Examples

```
feat(api): add streaming support to query endpoint

Implements server-sent events for long-running queries.
Clients can now receive partial results as they arrive
instead of waiting for full response.

Closes #234
```

```
fix(reconciler): skip requeue when object is being deleted

When DeletionTimestamp is set, reconciliation should exit
early rather than attempting to create child resources.
This prevents spurious errors during graceful deletion.
```

```
refactor(pkg/retry): extract exponential backoff to shared util

Three controllers were duplicating identical retry logic.
Centralizes into pkg/retry with configurable options.
No behavioral changes.
```
