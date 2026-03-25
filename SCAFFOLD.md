# agent-scaffold

A template repo for bootstrapping projects with a full AI agent pipeline:
Claude Code (Opus) as lead + Codex (o3) as independent reviewer via MCP.

Every project gets a structured lifecycle from idea to deploy, with quality
gates at every step.

## Quick Start

```bash
git clone https://github.com/YOUR_USERNAME/agent-scaffold.git

# New project
./init my-project
cd my-project
claude /kickoff

# Existing project
cd existing-project
../agent-scaffold/init
```

## The Lifecycle

```
/kickoff ──→ /brainstorm ──→ /grill-me ──→ /architect ──→ /implement ──→ /ship
   │              │              │              │              │           │
   ▼              ▼              ▼              ▼              ▼           ▼
CLAUDE.md    ADRs in        Spec in        Architecture   Code +      Ship
filled in    docs/adr/      docs/specs/    doc + ADRs     tests       report
```

| Command | What it does |
|---------|-------------|
| `/kickoff` | Interactive project setup — defines identity, tech stack, constraints |
| `/brainstorm` | Explore approaches, research prior art, write ADRs |
| `/grill-me` | Adversarial requirements interview → battle-tested spec |
| `/spec` | Quick spec from a brief (less interactive than grill-me) |
| `/architect` | System design, module breakdown, build order, ADRs |
| `/implement` | Build from spec, module by module, with test + review gates |
| `/run` | Build, start, evaluate output, auto-iterate until spec-correct (incl. visual/image) |
| `/feature` | Add a feature to an existing project (lightweight spec → build → review) |
| `/audit` | Deep bug hunt — Claude + Codex independently audit the codebase |
| `/review` | Comprehensive code review via Codex MCP |
| `/refactor` | Systematic refactoring with safety nets (tests first) |
| `/debug` | Structured debugging — hypothesize, verify, fix, test |
| `/ship` | Pre-deploy validation checklist + ship report |

> 📖 **New here?** Read [docs/GETTING-STARTED.md](docs/GETTING-STARTED.md) for a full walkthrough.

## What's Included

```
.claude/
├── commands/
│   ├── kickoff.md       # Project setup interview
│   ├── brainstorm.md    # Multi-perspective exploration
│   ├── grill-me.md      # Adversarial spec builder
│   ├── spec.md          # Quick spec from brief
│   ├── architect.md     # System design + ADRs
│   ├── implement.md     # Phased build with gates
│   ├── run.md           # Build, run, evaluate, iterate loop
│   ├── feature.md       # Add feature to existing project
│   ├── audit.md         # Deep bug hunt (Claude + Codex)
│   ├── review.md        # Code review via Codex
│   ├── refactor.md      # Safe refactoring workflow
│   ├── debug.md         # Structured debugging
│   └── ship.md          # Deploy validation checklist
├── agents/
│   ├── codex-review.md  # Independent review via Codex MCP
│   ├── test-writer.md   # Test generation (Sonnet, cheap)
│   ├── rule-enforcer.md # Code rule validation
│   └── researcher.md    # Prior art + library research
├── skills/
│   ├── clean-code.md    # Coding patterns and transforms
│   ├── error-handling.md # Error handling patterns
│   ├── git-conventions.md # Commit, branch, PR workflow
│   └── worklog.md       # Shared agent memory conventions
├── hooks/
│   └── pre-commit.md    # Blocks rule-violating commits
└── config.toml          # MCP servers (Codex, Playwright, etc.)

docs/
├── GETTING-STARTED.md   # Step-by-step first-time guide
├── templates/
│   ├── spec.md          # 15-section spec template
│   ├── adr.md           # Architecture Decision Record template
│   └── session-log.md   # Session log template
├── worklog/             # Shared agent memory
│   ├── INDEX.md         # Conventions and structure
│   ├── plans/           # Implementation, feature, architecture plans
│   ├── checklists/      # Ship, audit, review checklists
│   ├── sessions/        # Session logs (what was done, decisions, next steps)
│   └── reviews/         # Code and spec review reports
├── specs/               # Approved specifications
└── adr/                 # Architecture decisions

CLAUDE.md                # Project brain — context, rules, workflow
PROGRESS.md              # Phase tracker with checklists
init                     # Bootstrap script
grill-me                 # Shortcut: ./grill-me "description"
```

## Agent Architecture

| Agent | Model | Role |
|-------|-------|------|
| Lead (you + Claude) | Opus | Architecture, specs, complex logic |
| codex-review | Codex o3 via MCP | Independent code + spec review |
| test-writer | Sonnet | Write tests (bounded, cheap) |
| rule-enforcer | Sonnet | Validate code rules |
| researcher | Sonnet | Library eval, prior art search |

## Code Rules (enforced automatically)

1. One class per file
2. Max 4 params per function (use DTO beyond that)
3. Max 50 lines per function
4. No bumpy road (max 3 nesting levels)
5. Max 500 lines per file
6. Docstrings on all public functions
7. Type hints on all signatures
8. Named constants only (no magic numbers)
9. Composition over inheritance
10. Explicit error handling (no bare except)

## MCP Servers

Edit `.claude/config.toml`. Pre-configured options:

| Server | Purpose | Status |
|--------|---------|--------|
| **codex** | Code review via Codex CLI | Enabled (needs API key) |
| playwright | Browser automation | Commented |
| context7 | Live library docs | Commented |
| postgres | Database access | Commented |
| sqlite | SQLite access | Commented |
| gws | Google Workspace | Commented |
| sentry | Error tracking | Commented |
| linear | Issue tracking | Commented |
| ai-cli | Multi-model agents | Commented |

## Prerequisites

```bash
# Required
npm install -g @anthropic-ai/claude-code

# Recommended (for review pipeline)
npm install -g @openai/codex

# MCP bridge (or use npx, auto-handled)
# npm install -g codex-as-mcp
```

## Customization

- **Add commands**: `.claude/commands/your-command.md`
- **Add agents**: `.claude/agents/your-agent.md`
- **Add skills**: `.claude/skills/your-skill.md` (use `globs` for auto-apply)
- **Change rules**: Edit CLAUDE.md + `rule-enforcer.md`
- **Add MCP servers**: Edit `.claude/config.toml`

## License

MIT
