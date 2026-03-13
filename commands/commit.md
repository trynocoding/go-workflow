---
name: commit
description: Generate and execute a Conventional Commit for staged changes. Reads staged diff, writes a properly scoped commit message, and runs git commit. Stage your changes with git add first.
disable-model-invocation: true
allowed-tools:
  - Bash
  - Read
---

Load and follow the `git-conventional-commits` skill.

Here are the currently staged changes:

```
!`git diff --cached --stat`
```

Full diff:

```
!`git diff --cached`
```

Current branch: !`git branch --show-current`

Recent commits for context (last 5):
```
!`git log --oneline -5`
```

Based on the staged diff above:

1. Analyze all changed files and understand what changed and why
2. Determine the correct `type` and `scope` from the conventional commits skill
3. Write a subject line: imperative mood, lowercase, ≤72 chars total
4. If the change is complex or non-obvious, write a body paragraph explaining motivation
5. If there are breaking changes, add `!` and `BREAKING CHANGE:` footer
6. Run `git commit -m "<message>"` (with `-m` for body if needed: `git commit -m "<subject>" -m "<body>"`)
7. Show the final commit hash and message

Do not ask for confirmation. Commit immediately with the best message based on the diff.

If there is nothing staged (`git diff --cached` is empty), say so and stop.
