---
name: pr
description: "Generate a pull request description for the current branch. Compares against main by default, or specify a base branch: /go-workflow:pr develop"
argument-hint: "[base-branch]"
disable-model-invocation: true
allowed-tools:
  - Bash
  - Read
  - Grep
  - Glob
---

Load and follow the `git-conventional-commits` and `go-code-review` skills for context.

Current branch: !`git branch --show-current`
Base branch: ${ARGUMENTS:-main}

Commits in this PR:
```
!`git log origin/${ARGUMENTS:-main}..HEAD --oneline`
```

Files changed:
```
!`git diff origin/${ARGUMENTS:-main}..HEAD --name-status`
```

Full diff:
```
!`git diff origin/${ARGUMENTS:-main}..HEAD`
```

Generate a pull request description in the following format. Output only the markdown — no preamble:

---

## Summary

<!-- 2-3 sentences: what does this PR do and why? -->

## Changes

<!-- Bullet list of key changes, grouped by area if large -->

## Type of Change

- [ ] Bug fix (non-breaking change that fixes an issue)
- [ ] New feature (non-breaking change that adds functionality)
- [ ] Breaking change (fix or feature that changes existing behavior)
- [ ] Refactor (no functional changes)
- [ ] Performance improvement
- [ ] Tests / Documentation

## How to Test

<!-- Specific steps to verify this works correctly -->

## Go-Specific Checklist

- [ ] Error handling: all errors wrapped with context using `%w`
- [ ] No goroutine leaks: all goroutines have a stop mechanism
- [ ] Context propagation: `ctx` passed to all I/O operations
- [ ] Resources closed: `defer Close()` in place for all opened resources
- [ ] Tests cover the happy path and key error cases

## Related Issues

<!-- Closes #N or N/A -->

---

Check the appropriate type-of-change boxes based on the actual diff content. Fill in all sections with specific detail from the diff, not generic placeholders.
