---
name: git-conventions
description: Git workflow conventions. Apply when committing, branching, or creating PRs.
---

## Commit Messages

Format: `type: description`

Types:
- `feat:` — new functionality
- `fix:` — bug fix
- `refactor:` — code restructuring, no behavior change
- `test:` — adding or updating tests
- `docs:` — documentation changes
- `chore:` — tooling, config, dependencies

Rules:
- Imperative mood: "add X" not "added X"
- Max 72 characters for first line
- Reference spec or ADR if applicable: `feat: add pricing engine (spec: price-scraper)`
- One logical change per commit

## Branching

- `feat/<slug>` — feature work
- `fix/<slug>` — bug fixes
- `refactor/<slug>` — refactoring
- `spike/<slug>` — exploratory work (may be discarded)

## Before Committing

1. Run tests
2. Run rule-enforcer
3. Review your own diff: `git diff --staged`
4. Write a meaningful commit message

## Before Merging

1. Codex review must pass
2. All tests green
3. PROGRESS.md updated
4. No unresolved TODOs in changed files
