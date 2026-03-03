---
name: changelog
description: "Generate a changelog between two refs. Usage: /go-workflow:changelog <from>..<to> (e.g. v1.2.0..v1.3.0 or v1.2.0..HEAD). Organizes commits by type into Keep a Changelog format."
argument-hint: "<from>..<to>"
disable-model-invocation: true
---

Generating changelog for range: $ARGUMENTS

Commits in range:
```
!`git log $ARGUMENTS --oneline --no-merges`
```

Commit details (with body):
```
!`git log $ARGUMENTS --no-merges --format="----%ncommit %H%nAuthor: %an%nDate: %ad%n%n%B"`
```

Tags for context:
```
!`git tag --sort=-version:refname | head -10`
```

Generate a changelog in Keep a Changelog format (https://keepachangelog.com). Output only the markdown:

---

## [VERSION] - DATE

### Breaking Changes
<!-- feat!: or BREAKING CHANGE commits only — omit section if none -->

### Added
<!-- feat: commits -->

### Fixed
<!-- fix: commits -->

### Changed
<!-- refactor:, perf: commits -->

### Deprecated
<!-- deprecation notices — omit if none -->

### Removed
<!-- removal of features — omit if none -->

### Internal
<!-- chore:, ci:, test:, docs: commits — keep brief, one line each -->

---

Rules:
- Extract VERSION from the `to` ref in the range argument (e.g. `v1.3.0`) or use `Unreleased` if it's `HEAD`
- Use today's date for DATE if `HEAD`, otherwise use the tag date from git
- Write each entry from the user's perspective (what changed for them), not the implementer's perspective
- Group related commits if they form a coherent feature
- Skip fixup commits, merge commits, and CI-only changes from user-facing sections
- For breaking changes: include migration instructions
- Omit empty sections entirely
