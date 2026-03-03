---
name: git-branch-strategy
description: Use when creating branches, starting new features, or preparing to merge. Enforces consistent branch naming and workflow discipline for Go projects.
license: MIT
compatibility: claude-code, opencode
metadata:
  workflow: git
  language: go
---

# Git Branch Strategy

## Branch Naming Convention

```
<type>/<short-description>
```

### Types

| Type | Purpose | Example |
|---|---|---|
| `feat/` | New feature | `feat/streaming-query-api` |
| `fix/` | Bug fix | `fix/controller-nil-panic` |
| `refactor/` | Restructure, no behavior change | `refactor/extract-retry-util` |
| `perf/` | Performance improvement | `perf/reduce-reconcile-allocs` |
| `test/` | Tests only | `test/integration-crd-lifecycle` |
| `docs/` | Documentation | `docs/operator-design-guide` |
| `chore/` | Deps, tooling, CI | `chore/upgrade-controller-runtime` |
| `release/` | Release preparation | `release/v1.4.0` |
| `hotfix/` | Urgent production fix | `hotfix/memory-leak-in-watch` |

### Rules

- **Lowercase only**, words separated by hyphens
- **Concise**: 2–5 words max in description
- **No generic names**: `fix/bug`, `feat/feature`, `test/tests` are banned
- Derived from the issue/task when one exists: `feat/GH-123-streaming-api`

## Protected Branches

- `main` — production-ready, requires PR + review
- `develop` (if used) — integration branch

**Never commit directly** to `main`. Always via PR.

## Workflow

### Starting a Feature

```bash
git checkout main
git pull origin main
git checkout -b feat/my-feature
```

### During Development

- Commit frequently with atomic commits (see `git-conventional-commits` skill)
- Keep branch focused — one feature/fix per branch
- Rebase onto `main` if long-lived (not merge):

```bash
git fetch origin
git rebase origin/main
```

### Before Creating PR

```bash
# Verify you're on the right branch
git branch --show-current

# Check your commits are clean
git log origin/main..HEAD --oneline

# Run tests
go test ./...

# Squash fixup commits if needed
git rebase -i origin/main
```

### Branch Cleanup

After merge, delete branch:
```bash
git branch -d feat/my-feature
git push origin --delete feat/my-feature
```

## Commit Message ↔ Branch Alignment

Branch type should match commit type:
- Branch `feat/*` → commits start with `feat:`
- Branch `fix/*` → commits start with `fix:`
- Exception: a `feat/*` branch may contain `test:` and `docs:` commits alongside `feat:` commits

## When to Split a Branch

Split into separate branches when you find yourself:
- Fixing an unrelated bug while implementing a feature
- Making refactors that are prerequisite to the feature
- Working on two independent features simultaneously

**Rule of thumb**: If the PR description needs "Also, I fixed..." — split it.
