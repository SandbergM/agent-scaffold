# agent-scaffold

A template repo for bootstrapping projects with a full AI agent pipeline:
Claude Code (Opus) as lead + Codex (o3) as independent reviewer via MCP.

## Quick Start

```bash
git clone https://github.com/SandbergM/agent-scaffold.git my-project
cd my-project
rm -rf .git && git init
.claude/init
claude /kickoff
```

## The Lifecycle

```
/kickoff → /brainstorm → /grill-me → /pre-mortem → /architect → /implement → /run → /ship
                              ↕
                          /discuss  (Claude ↔ Codex dialogue at any point)
```

| Command | What it does |
|---------|-------------|
| `/kickoff` | Interactive project setup — identity, tech stack, constraints |
| `/brainstorm` | Explore approaches, research prior art, write ADRs |
| `/grill-me` | Adversarial requirements interview → battle-tested spec |
| `/pre-mortem` | "Imagine this failed. Why?" Stress-tests specs |
| `/discuss` | Multi-round Claude ↔ Codex dialogue to debate and converge |
| `/spec` | Quick spec from a brief |
| `/architect` | System design, module breakdown, build order |
| `/implement` | Build from spec with test + review gates |
| `/run` | Build, start, evaluate, auto-iterate until spec-correct |
| `/feature` | Add a feature to an existing project |
| `/audit` | Deep bug hunt — Claude + Codex |
| `/review` | Fan-out: Codex + simplifier + rules + docs + security |
| `/refactor` | Safe refactoring (tests first) |
| `/debug` | Structured debugging |
| `/ship` | Pre-deploy checklist + ship report |

> 📖 Read `.claude/GETTING-STARTED.md` for a full walkthrough.

## Structure

Everything lives in `.claude/` except `CLAUDE.md` (root):

```
CLAUDE.md                        # Project brain — stays in root
LICENSE                          # MIT
.claude/
├── init                         # Bootstrap script (creates dirs, checks tools)
├── grill-me                     # Shortcut: .claude/grill-me "description"
├── commands/ (15)               # Slash commands
├── agents/ (7)                  # Sub-agents
│   ├── codex-review.md          pentester.md       simplifier.md
│   ├── doc-enforcer.md          test-writer.md     rule-enforcer.md
│   └── researcher.md
├── skills/ (8)                  # Auto-applied coding skills
│   ├── clean-code.md            dependency-injection.md
│   ├── error-handling.md        fastapi-structure.md
│   ├── git-conventions.md       task-delegation.md
│   └── worklog.md
├── hooks/
│   └── pre-commit.md
├── templates/                   # Spec, ADR, session log templates
├── worklog/                     # Shared agent memory
│   ├── plans/  checklists/  sessions/  reviews/
├── config.toml                  # MCP servers
├── PROGRESS.md                  # Phase tracker
├── SCAFFOLD.md                  # This file
├── GETTING-STARTED.md           # Step-by-step guide
└── .gitignore                   # Copied to root by init
docs/                            # Created by init
├── specs/                       # Approved specifications
└── adr/                         # Architecture decisions
```

## MCP Servers

| Server | Purpose | Default |
|--------|---------|---------|
| **codex** | Codex CLI as review agent | On (needs API key) |
| **context7** | Live library docs | On (free) |
| playwright | Browser automation | Off |
| postgres / sqlite | Database | Off |
| gws | Google Workspace | Off |
| sentry | Error tracking | Off |
| ai-cli | Multi-model agents | Off |

## Code Rules

1. One class per file — file name = class name in snake_case
2. Max 4 params (use DTO beyond that)
3. Max 50 lines per function
4. Max 3 nesting levels
5. Max 500 lines per file
6. Meaningful docstrings (no filler)
7. Type hints everywhere
8. Named constants only
9. Composition over inheritance
10. Explicit error handling
11. Dependencies injected

## License

MIT
