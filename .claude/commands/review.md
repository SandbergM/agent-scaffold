---
name: review
description: Run a comprehensive code review using Codex as the primary reviewer. Checks code rules, logic, security, and spec compliance.
---

Perform a comprehensive review of the current changes.

## Worklog

- **Before:** Read `docs/worklog/reviews/` for past review findings. Read `docs/worklog/sessions/` to understand what was changed and why.
- **After:** Save review report to `docs/worklog/reviews/YYYY-MM-DD-review-<slug>.md` and session log to `docs/worklog/sessions/`.

## Process

1. Run `git diff` to see what changed (or diff against main).
2. Invoke the codex-review agent with the diff and relevant spec.
3. Additionally check against CLAUDE.md code rules:
   - [ ] One class per file
   - [ ] Max 4 params per function
   - [ ] Max 50 lines per function
   - [ ] No bumpy road code
   - [ ] Max 500 lines per file
   - [ ] All public functions have docstrings
4. Compile findings into a review report.
5. If issues found: list them with file:line references and suggested fixes.
6. If clean: confirm and suggest committing.
