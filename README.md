# go-workflow

> Git workflow skills, commands, and agents for Go developers — built as a Claude Code plugin.

Automates and standardizes the git workflow you repeat every day: writing commit messages, reviewing changes, explaining commits by ID, generating PR descriptions, and producing changelogs.

## Installation

### Via Marketplace (recommended)

Register the marketplace, then install the plugin:

```
/plugin marketplace add trynocoding/go-workflow-marketplace
/plugin install go-workflow@go-workflow-marketplace
```

### For local development / testing

Clone the repo and load it directly with `--plugin-dir`:

```bash
git clone https://github.com/trynocoding/go-workflow
claude --plugin-dir ./go-workflow
```

Once installed, all commands are namespaced as `/go-workflow:<command>`.

---

## What's Inside

### Skills (auto-loaded as context)

| Skill | Triggers When |
|---|---|
| `git-conventional-commits` | Any commit-related task |
| `go-code-review` | Reviewing Go code changes |
| `git-branch-strategy` | Creating branches, starting features |
| `use-modern-go` | Writing new Go code |

Skills have `user-invocable: false` — Claude loads them automatically when relevant. You don't invoke them directly.

### Commands (`/` to invoke)

| Command | Usage | What it does |
|---|---|---|
| `/go-workflow:commit` | `/go-workflow:commit` | Reads staged diff → generates conventional commit msg → executes `git commit` |
| `/go-workflow:review` | `/go-workflow:review` or `/go-workflow:review 3` | Reads last N commits → full Go-aware code review |
| `/go-workflow:explain` | `/go-workflow:explain abc1234` | Reads `git show <sha>` → explains what changed, why, and impact |
| `/go-workflow:pr` | `/go-workflow:pr` or `/go-workflow:pr develop` | Reads branch diff → generates structured PR description |
| `/go-workflow:changelog` | `/go-workflow:changelog v1.2.0..v1.3.0` | Reads commit range → generates Keep a Changelog formatted output |

All commands have `disable-model-invocation: true` — only you trigger them, never Claude automatically.

### Agent

| Agent | How to use |
|---|---|
| `git-master` | Complex git operations: rebase, squash, bisect, blame analysis, history search |

---

## Usage Examples

### Daily commit workflow
```
# Stage your changes first
git add -p

# Let AI write and execute the commit
/go-workflow:commit
```

### Review before pushing
```
/go-workflow:review 2     # review last 2 commits
```

### Understand a colleague's change
```
/go-workflow:explain d4f8a9c
```

### Write the PR description
```
/go-workflow:pr           # compares current branch against main
/go-workflow:pr develop   # compares against develop
```

### Release prep
```
/go-workflow:changelog v1.2.0..HEAD
```

### Complex git work
```
Use the git-master subagent to squash the last 4 commits and reorder them
Use the git-master subagent to find when this race condition was introduced: func (w *Watcher) handleEvent
```

---

## Design Principles

- **Full automation by default** — commands execute, not just suggest
- **Shell injection** — commands read actual git state at invocation time using `` !`command` `` syntax
- **Go-aware** — code review and commit conventions are tuned for Go idioms, not generic
- **Composable** — skills layer on top of each other (commit command loads conventional-commits skill, agent preloads both skills)
- **Plugin namespaced** — commands use `/go-workflow:<name>` to avoid conflicts with your other skills

---

## Project Structure

```
go-workflow/
├── .claude-plugin/
│   ├── plugin.json              # Claude Code plugin metadata
│   └── marketplace.json         # Local dev marketplace (for --plugin-dir testing)
├── skills/
│   ├── git-conventional-commits/
│   │   └── SKILL.md             # Conventional Commits for Go projects
│   ├── go-code-review/
│   │   ├── SKILL.md             # Go-specific code review checklist
│   │   └── reference.md         # Versioned modern Go syntax flags (1.13–1.26)
│   ├── git-branch-strategy/
│   │   └── SKILL.md             # Branch naming and workflow
│   └── use-modern-go/
│       └── SKILL.md             # Modern Go syntax reference (write-time)
├── commands/
│   ├── commit.md                # /go-workflow:commit
│   ├── review.md                # /go-workflow:review [N]
│   ├── explain.md               # /go-workflow:explain <sha>
│   ├── pr.md                    # /go-workflow:pr [base-branch]
│   └── changelog.md             # /go-workflow:changelog <from>..<to>
└── agents/
    └── git-master.md            # git-master subagent
```

---

## Contributing

Skills and commands live directly in this repo. To contribute:

1. Fork and create a branch (`feat/my-skill`)
2. Add your skill/command following the existing format
3. Test it with `claude --plugin-dir ./go-workflow`
4. Submit a PR with a description of what it does and when it triggers

For skills: make sure `name` in frontmatter matches the directory name exactly.
For commands: test the shell injection (`` !`...` `` syntax) with your target arguments.

---

## License

MIT
