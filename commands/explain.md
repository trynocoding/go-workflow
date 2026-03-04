---
name: explain
description: "Explain what a commit changed, why, and its impact. Usage: /go-workflow:explain <commit-sha>"
argument-hint: "<commit-sha>"
disable-model-invocation: true
allowed-tools:
  - Bash
  - Read
---

Explaining commit: $ARGUMENTS

Commit metadata:
```
!`git show $ARGUMENTS --stat --format="Author: %an <%ae>%nDate: %ad%nMessage: %s%n%n%b"`
```

Full diff:
```
!`git show $ARGUMENTS`
```

Git context (commits around it):
```
!`git log $ARGUMENTS~2..$ARGUMENTS --oneline --graph`
```

Provide a clear technical explanation structured as follows:

## What Changed
- List each file changed and what specifically was modified (not just "file was edited" — explain the actual logic change)
- Group related changes together

## Why (Motivation)
- Based on the commit message and diff context, explain the motivation
- What problem does this solve? What behavior changed?

## Impact & Side Effects
- What does this change affect at runtime?
- Any API changes callers need to know about?
- Any performance implications?
- Anything that could break if downstream code isn't updated?

## Go-Specific Notes
- Any concurrency implications?
- Error handling changes?
- Interface changes?
- Notable patterns used or introduced?

Keep explanations technical and precise. Use file:line references for specific findings.
