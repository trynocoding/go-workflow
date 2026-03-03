---
description: "Review recent Go code changes for correctness, idiomatic patterns, error handling, and concurrency safety. Usage: /review [N] where N is number of commits (default: 1). Or: /review <base-sha>..<head-sha>"
---

Load and follow the `go-code-review` skill.

Reviewing the following changes:

```
!`git log --oneline -${1:-1}`
```

Changed files:
```
!`git diff HEAD~${1:-1} --name-status`
```

Full diff:
```
!`git diff HEAD~${1:-1}`
```

Current branch: !`git branch --show-current`
Repository: !`basename $(git rev-parse --show-toplevel)`

Perform a thorough code review following the `go-code-review` skill. Cover all severity levels:
- 🔴 Critical issues that must be fixed
- 🟡 Important improvements that should be addressed
- 🔵 Minor idiomatic suggestions
- ✅ Good patterns worth noting

For each finding, include the exact file:line reference and a concrete fix suggestion.

End with a summary and overall assessment: Ready to merge / Needs fixes / Major revision needed.
