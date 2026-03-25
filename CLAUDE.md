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
/kickoff → /brainstorm → /grill-me → /pre-mortem → /architect → /implement → /run → /ship → /retro
                              ↕
                          /discuss  (Claude ↔ Codex dialogue at any point)
```

### Existing project (add feature)
```
/feature "description" → /run → /review → /ship
```

### Maintenance
```
/audit      — deep bug hunt with Claude + Codex
/debug      — structured debugging for a specific issue
/refactor   — safe restructuring with tests first
/pre-mortem — stress-test a spec or plan for hidden risks
/discuss    — Claude ↔ Codex dialogue on any topic
/retro      — retrospective: what worked, what didn't, learnings
```

1. **Kickoff** — Project identity, tech stack, constraints, mcp.json generation
2. **Brainstorm** — Explore approaches, research prior art, weigh trade-offs
3. **Grill-me** — Adversarial requirements interview
4. **Pre-mortem** — "Imagine this failed. Why?" Finds blind spots
5. **Discuss** — Multi-round Claude ↔ Codex dialogue to debate and converge
6. **Architect** — System design, write ADRs for key decisions
7. **Implement** — Build module by module with test + review gates
8. **Run** — Build, start, evaluate output, iterate until spec-correct
9. **Review** — Fan-out: Codex + simplifier + rules + docs + security + UX
10. **Ship** — Pre-deploy checklist, CI/CD generation, final validation
11. **Feature** — Add to an existing project without breaking it
12. **Audit** — Claude + Codex deep-dive for bugs + security
13. **Retro** — Post-project: what worked, what to improve next time

Never skip from Kickoff to Implement. The middle steps exist to prevent rework.

## Agent Memory

All agents read and write to `.claude/worklog/`. This is the shared memory
that persists across sessions. Agents MUST:

1. **Read** recent session logs before starting any work
2. **Write** a session log after completing any command
3. **Check** for contradictions with past decisions before making new ones

See `.claude/worklog/INDEX.md` for full conventions.

## Directory Layout

```
CLAUDE.md              # Project brain (this file) — stays in root
docs/
  specs/               # Approved specifications
  adr/                 # Architecture Decision Records
.claude/
  commands/            # Slash commands (/grill-me, /discuss, etc.)
  agents/              # Subagents (codex-review, pentester, etc.)
  skills/              # Reusable coding skills
  hooks/               # Pre-commit hooks
  templates/           # Templates for specs, ADRs, session logs
  worklog/             # Shared agent memory
    plans/             # Implementation plans, feature plans, architecture notes
    checklists/        # Ship, audit, and review checklists
    sessions/          # Session logs — what was done, decisions, next steps
    reviews/           # Code/spec/security review reports
  config.toml          # MCP server configuration
  PROGRESS.md          # Phase tracker with checklists
src/ or app/           # Application source
tests/                 # Mirrors source structure
```

## Agent Configuration

| Role | Model | Purpose |
|------|-------|---------|
| Lead | Opus | Architecture, spec iteration, complex logic |
| Review | Codex (o3) via MCP | Independent code + spec review |
| Product Manager | Sonnet | Scope control, prioritization, acceptance criteria |
| Pentester | Opus + Codex | Security audit, auth/token analysis, OWASP |
| DevOps | Sonnet | Infra review, Dockerfiles, deployment readiness |
| Adversarial Tester | Sonnet | Tries to BREAK the code — fuzzing, edge cases, chaos |
| Simplifier | Sonnet | Flags unnecessary complexity and overengineering |
| Doc Enforcer | Sonnet | Ensures meaningful docs, catches AI filler |
| UX Reviewer | Sonnet | Usability, consistency, accessibility, error messages |
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
