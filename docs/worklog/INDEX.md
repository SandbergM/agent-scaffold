# Worklog

This folder is the **shared memory** for all agents. Every plan, checklist,
review, and session log is saved here so any agent can see what's been
done before.

**Agents: ALWAYS read relevant worklog files before starting work.**
**Agents: ALWAYS write your output to the appropriate subfolder.**

## Structure

```
docs/worklog/
├── plans/        # Implementation plans, feature plans, architecture notes
├── checklists/   # Active and completed checklists (ship, audit, review)
├── sessions/     # Session logs — what was done, decisions made, blockers hit
└── reviews/      # Code and spec review reports from Claude and Codex
```

## Naming Convention

All files use this format:

```
YYYY-MM-DD-<command>-<slug>.md
```

Examples:
- `plans/2026-03-25-implement-price-scraper.md`
- `checklists/2026-03-25-ship-v1.md`
- `sessions/2026-03-25-grill-me-price-scraper.md`
- `reviews/2026-03-25-audit-full-codebase.md`

## What Goes Where

### plans/
- Implementation plans from `/implement`
- Feature plans from `/feature`
- Architecture plans from `/architect`
- Brainstorm summaries from `/brainstorm`
- Refactoring plans from `/refactor`

### checklists/
- Ship checklists from `/ship`
- Audit checklists from `/audit`
- Feature completion checklists from `/feature`
- Any TODO lists generated during work

### sessions/
- Session summaries: what was done, what was decided, what's next
- Created at the END of every command that does meaningful work
- Includes: command used, files changed, decisions made, open questions

### reviews/
- Codex review reports
- Claude audit findings
- Cross-reference analysis from `/audit`
- Rule-enforcer reports

## For Agents

When starting any command:

1. Read `docs/worklog/sessions/` — check the last 3-5 session logs
2. Read any relevant plans or checklists in the other subfolders
3. Use this context to avoid re-doing work or contradicting past decisions

When finishing any command:

1. Save your primary output to the appropriate subfolder
2. Write a session log to `sessions/` with:
   - Date and command used
   - What was accomplished
   - Decisions made (with reasoning)
   - Files created or modified
   - Open questions or next steps
   - Blockers encountered
