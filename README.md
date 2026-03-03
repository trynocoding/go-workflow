# go-workflow

> Git workflow skills, commands, and agents for Go developers — built as a Claude Code plugin.

Automates and standardizes the git workflow you repeat every day: writing commit messages, reviewing changes, explaining commits by ID, generating PR descriptions, and producing changelogs.

## Installation

### Claude Code (Plugin Marketplace)

```
/plugin marketplace add yourusername/go-workflow-marketplace
/plugin install go-workflow@go-workflow-marketplace
```

### Claude Code (Direct — Local or any repo)

Clone the repo into your project or globally, then reference in Claude Code settings:

```bash
git clone https://github.com/yourusername/go-workflow ~/.claude/plugins/go-workflow
```

### OpenCode

Add to your `opencode.json`:

```json
{
  "plugin": ["path/to/go-workflow"]
}
```

Or tell OpenCode:

```
Fetch and follow instructions from https://raw.githubusercontent.com/yourusername/go-workflow/main/.opencode/INSTALL.md
```

---

## What's Inside

### Skills (auto-loaded as context)

| Skill | Triggers When |
|---|---|
| `git-conventional-commits` | Any commit-related task |
| `go-code-review` | Reviewing Go code changes |
| `git-branch-strategy` | Creating branches, starting features |

### Commands (`/` to invoke)

| Command | Usage | What it does |
|---|---|---|
| `/commit` | `/commit` | Reads staged diff → generates conventional commit msg → executes `git commit` |
| `/review` | `/review` or `/review 3` | Reads last N commits → full Go-aware code review |
| `/explain` | `/explain abc1234` | Reads `git show <sha>` → explains what changed, why, and impact |
| `/pr` | `/pr` or `/pr develop` | Reads branch diff → generates structured PR description |
| `/changelog` | `/changelog v1.2.0..v1.3.0` | Reads commit range → generates Keep a Changelog formatted output |

### Agent

| Agent | How to use |
|---|---|
| `@git-master` | Complex git operations: rebase, squash, bisect, blame analysis, history search |

---

## Usage Examples

### Daily commit workflow
```
# Stage your changes first
git add -p

# Let AI write and execute the commit
/commit
```

### Review before pushing
```
/review 2     # review last 2 commits
```

### Understand a colleague's change
```
/explain d4f8a9c
```

### Write the PR description
```
/pr           # compares current branch against main
/pr develop   # compares against develop
```

### Release prep
```
/changelog v1.2.0..HEAD
```

### Complex git work
```
@git-master I need to squash the last 4 commits and reorder them
@git-master find when this race condition was introduced: func (w *Watcher) handleEvent
```

---

## Design Principles

- **Full automation by default** — commands execute, not just suggest
- **Shell injection** — commands read actual git state at invocation time, not from your description of it
- **Go-aware** — code review and commit conventions are tuned for Go idioms, not generic
- **Composable** — skills layer on top of each other (commit command loads conventional-commits skill, agent loads both skills)
- **Cross-tool** — same skills work in Claude Code, OpenCode, and other compatible tools

---

## Project Structure

```
go-workflow/
├── .claude-plugin/
│   └── plugin.json              # Claude Code plugin metadata
├── skills/
│   ├── git-conventional-commits/
│   │   └── SKILL.md             # Conventional Commits for Go projects
│   ├── go-code-review/
│   │   └── SKILL.md             # Go-specific code review checklist
│   └── git-branch-strategy/
│       └── SKILL.md             # Branch naming and workflow
├── commands/
│   ├── commit.md                # /commit
│   ├── review.md                # /review [N]
│   ├── explain.md               # /explain <sha>
│   ├── pr.md                    # /pr [base-branch]
│   └── changelog.md             # /changelog <from>..<to>
└── agents/
    └── git-master.md            # @git-master
```

---

## Contributing

Skills and commands live directly in this repo. To contribute:

1. Fork and create a branch (`feat/my-skill`)
2. Add your skill/command following the existing format
3. Test it in your project
4. Submit a PR with a description of what it does and when it triggers

For skills: make sure `name` in frontmatter matches the directory name exactly.
For commands: test the shell injection (`!` backtick syntax) in your target tool.

---

## License

MIT
