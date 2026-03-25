---
name: review
description: Run a comprehensive code review using Codex as the primary reviewer. Checks code rules, logic, security, and spec compliance.
---

Perform a comprehensive review of the current changes.

## Worklog

- **Before:** Read `.claude/worklog/reviews/` for past review findings. Read `.claude/worklog/sessions/` to understand what was changed and why.
- **After:** Save review report to `.claude/worklog/reviews/YYYY-MM-DD-review-<slug>.md` and session log to `.claude/worklog/sessions/`.

## Process

Fan out to multiple agents for independent analysis, then combine:

1. Run `git diff` to see what changed (or diff against main).
2. **Fan-out reviews** (delegate in parallel where possible):
   - codex-review agent → bugs, logic errors, spec compliance
   - simplifier agent → unnecessary complexity, overengineering
   - rule-enforcer agent → code conventions, file naming
   - doc-enforcer agent → documentation quality (filler vs useful)
   - product-manager agent → scope creep, acceptance criteria met
   - pentester agent → security issues (if auth/input handling changed)
   - devops agent → infra config (if Docker/deploy/env files changed)
   - ux-reviewer agent → usability, accessibility (if UI/templates changed)
   - adversarial-tester agent → edge cases, chaos tests (on critical paths)
3. Additionally check against CLAUDE.md code rules:
   - [ ] One class per file, file name matches class in snake_case
   - [ ] Max 4 params per function
   - [ ] Max 50 lines per function
   - [ ] No bumpy road code
   - [ ] Max 500 lines per file
   - [ ] Meaningful docstrings (not filler)
   - [ ] Dependencies injected, not hardcoded
4. **Combine** findings from all agents. Deduplicate. Resolve contradictions.
   Priority: security > correctness > simplicity > documentation > style.
5. If issues found: list them with file:line references and suggested fixes.
6. If clean: confirm and suggest committing.
