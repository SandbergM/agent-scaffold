# CLAUDE.md

## Project

<!-- ⚡ Run `claude /kickoff` to fill this in interactively -->

**Name:** <!-- project name -->
**One-liner:** <!-- one sentence: what it does and for whom -->
**Status:** 🟡 Discovery <!-- 🟡 Discovery | 🔵 Spec | 🟣 Build | 🟢 Ship -->

## Problem

<!-- What pain exists? Who feels it? Why now? -->

## Tech Stack

<!-- Filled in by /kickoff or manually -->
- **Language:**
- **Framework:**
- **Database:**
- **Infra/Deploy:**
- **Key libraries:**

## Architecture Principles

<!-- Project-specific. Examples: -->
<!-- - Event-driven, no synchronous cross-service calls -->
<!-- - All state in Postgres, services are stateless -->
<!-- - Feature flags for everything behind /api/v2 -->

## Code Rules

These are enforced by the `rule-enforcer` agent and the pre-commit hook.

1. One class per file — file name matches class name in snake_case
   (`UserService` → `user_service.py`, `MysqlClient` → `mysql_client.py`)
2. Max 4 params per function — use DTO/Pydantic model beyond that
3. Max 50 lines per function — extract helpers
4. No bumpy road — max 3 nesting levels, early returns, guard clauses
5. Max 500 lines per file — split modules
6. All public functions have docstrings
7. No magic numbers — named constants only
8. Composition over inheritance
9. Explicit error handling — no bare `except`, no swallowed errors
10. Type hints on all function signatures

## Workflow

### New project (greenfield)
```
/kickoff → /brainstorm → /grill-me → /architect → /implement → /run → /ship
```

### Existing project (add feature)
```
/feature "description" → /run → /review → /ship
```

### Maintenance
```
/audit    — deep bug hunt with Claude + Codex
/debug    — structured debugging for a specific issue
/refactor — safe restructuring with tests first
```

1. **Kickoff** — Set up project identity, tech stack, constraints
2. **Brainstorm** — Explore approaches, research prior art, weigh trade-offs
3. **Grill-me** — Nail down requirements through adversarial questioning
4. **Architect** — Design system, write ADRs for key decisions
5. **Implement** — Build module by module with test + review gates
6. **Run** — Build, start, evaluate output, iterate until spec-correct
7. **Review** — Codex reviews via MCP, rule-enforcer validates conventions
8. **Ship** — Pre-deploy checklist, final validation
9. **Feature** — Add to an existing project without breaking it
10. **Audit** — Claude + Codex deep-dive the codebase for bugs + security

Never skip from Kickoff to Implement. The middle steps exist to prevent rework.

## Agent Memory

All agents read and write to `docs/worklog/`. This is the shared memory
that persists across sessions. Agents MUST:

1. **Read** recent session logs before starting any work
2. **Write** a session log after completing any command
3. **Check** for contradictions with past decisions before making new ones

See `docs/worklog/INDEX.md` for full conventions.

## Directory Layout

```
src/               # Application source
tests/             # Mirrors src/ structure
docs/
  specs/           # Approved specifications
  adr/             # Architecture Decision Records
  templates/       # Templates for specs, ADRs, session logs
  worklog/         # Shared agent memory (plans, checklists, sessions, reviews)
    plans/         # Implementation plans, feature plans, architecture notes
    checklists/    # Ship, audit, and review checklists
    sessions/      # Session logs — what was done, decisions, next steps
    reviews/       # Code and spec review reports
scripts/           # Shell scripts and utilities
.claude/
  commands/        # Slash commands (/grill-me, /spec, etc.)
  agents/          # Subagents (codex-review, test-writer, etc.)
  skills/          # Reusable coding skills
  hooks/           # Pre-commit, post-implementation hooks
  config.toml      # MCP server configuration
```

## Agent Configuration

| Role | Model | Purpose |
|------|-------|---------|
| Lead | Opus | Architecture, spec iteration, complex logic |
| Review | Codex (o3) via MCP | Independent code + spec review |
| Tasks | Sonnet | Tests, formatting, bounded extraction |
| Research | Sonnet | Prior art, library evaluation |

## MCP Servers

See `.claude/config.toml`. Activate what the project needs.

## Conventions

- Branch names: `feat/<slug>`, `fix/<slug>`, `refactor/<slug>`
- Commit messages: imperative mood, max 72 chars, reference spec if applicable
- Every spec gets a slug, every ADR gets a sequence number
- PROGRESS.md is updated after every meaningful change
- No PR without Codex review pass
