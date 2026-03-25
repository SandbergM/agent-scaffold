---
name: worklog
description: Shared agent memory. Read before starting work, write when done. Applied to all commands automatically.
globs: [".claude/worklog/**/*.md"]
---

## The Worklog Convention

`.claude/worklog/` is the shared memory for all agents. It prevents duplicated
work, contradicted decisions, and lost context across sessions.

## Before Starting Any Command

**Best practice:** Run `/catchup` at the start of every new session.
It does everything below automatically and presents a briefing.

If running a command directly (not starting with /catchup), ALWAYS do this:

1. **Read recent sessions** — `ls .claude/worklog/sessions/` and read the
   last 3-5 files (sorted by date). Understand:
   - What was done recently
   - What decisions were made
   - What's pending or blocked

2. **Read relevant plans** — if you're implementing, check `plans/`.
   If you're reviewing, check `reviews/`. If you're shipping, check
   `checklists/`.

3. **Check for contradictions** — if a past session decided X and the
   user is now asking for not-X, flag it: "In the session on <date>,
   we decided <X> because <reason>. Want to change that?"

## After Completing Any Command

ALWAYS do this last:

1. **Save primary output** to the right subfolder:

   | Command | Save to |
   |---------|---------|
   | `/kickoff` | `sessions/` |
   | `/brainstorm` | `plans/` + `sessions/` |
   | `/grill-me` | `sessions/` |
   | `/pre-mortem` | `reviews/` + `sessions/` |
   | `/discuss` | `plans/` + `sessions/` |
   | `/architect` | `plans/` + `sessions/` |
   | `/implement` | `plans/` + `sessions/` |
   | `/run` | `sessions/` |
   | `/feature` | `plans/` + `sessions/` |
   | `/audit` | `reviews/` + `sessions/` |
   | `/review` | `reviews/` + `sessions/` |
   | `/refactor` | `plans/` + `sessions/` |
   | `/debug` | `sessions/` |
   | `/ship` | `checklists/` + `sessions/` |
   | `/retro` | `sessions/` |

2. **Write a session log** using `.claude/templates/session-log.md`:
   - Date and command
   - What was accomplished
   - Decisions with reasoning
   - Files changed
   - Open questions
   - Next steps

3. **Update PROGRESS.md** — check off completed items, add log entry.

## File Naming

```
YYYY-MM-DD-<command>-<slug>.md
```

Use today's date. If multiple sessions on the same day for the same
command, append a number: `2026-03-25-implement-auth-2.md`

## Reading Worklog Efficiently

Don't read every file every time. Prioritize:

- **Always**: last 3 session logs
- **If implementing**: latest plan in `plans/`
- **If reviewing**: latest reviews in `reviews/`
- **If shipping**: any open checklists in `checklists/`
- **If debugging**: recent session logs mentioning the same area
