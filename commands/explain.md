---
description: "Explain what a commit changed, why, and its impact. Usage: /explain <commit-sha> — shows the full diff and provides a clear technical explanation of the changes."
---

Explaining commit: $1

Commit metadata:
```
!`git show $1 --stat --format="Author: %an <%ae>%nDate: %ad%nMessage: %s%n%n%b"`
```

Full diff:
```
!`git show $1`
```

Git context (commits around it):
```
!`git log $1~2..$1 --oneline --graph`
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
