---
name: catchup
description: Get up to speed on a project. Reads CLAUDE.md, worklog, progress, specs, and recent git history. Use at the start of every new session, or when resuming after a break.
model: opus
---

You are resuming work on a project. Before doing ANYTHING else, get the
full picture of where things stand.

## Process

### 1. Read Project Context (always)

Read these files in order:
1. `CLAUDE.md` — project identity, tech stack, rules, workflow
2. `.claude/PROGRESS.md` — current phase, checklist status

### 2. Read Recent History

3. `.claude/worklog/sessions/` — read the **last 5 session logs** (newest first)
4. `.claude/worklog/plans/` — read the **most recent plan** (latest by date)
5. `.claude/worklog/reviews/` — read the **most recent review** (if any)
6. `.claude/worklog/checklists/` — check for **open/incomplete checklists**

### 3. Read Project State

7. `docs/specs/` — list all specs, note which are approved vs draft
8. `docs/adr/` — list all ADRs (don't read in full, just titles + status)
9. `git log --oneline -20` — last 20 commits
10. `git status` — any uncommitted changes?
11. `git branch` — which branch are we on?

### 4. Synthesize

Present a briefing to the user:

```
📋 PROJECT CATCHUP — <project name>

Phase: <current phase from PROGRESS.md>
Branch: <current git branch>
Last session: <date> — <what was done>

## Where We Are
<2-3 sentences: current state of the project>

## What Was Done Last
<bullet list from most recent session log>

## What's Next
<from the plan or session log's "next steps">

## Open Items
- <incomplete checklist items>
- <open questions from specs>
- <unresolved review findings>

## Uncommitted Changes
<git status summary, or "clean">

## Decisions to Remember
<key decisions from recent ADRs or session logs>
```

### 5. Ask

End with: **"What would you like to work on?"**

Suggest the logical next step based on the briefing:
- If in spec phase → "Continue with `/grill-me`?"
- If in build phase → "Continue with `/implement`?"
- If review findings are open → "Fix the review findings first?"
- If the last session hit a blocker → "Last time we got stuck on X. Want to tackle that?"

## When to Use

- **Start of every new session** — make this the first command
- **After a break** — days/weeks away from the project
- **After compaction** — if a long session compacted and lost context
- **Onboarding** — getting a collaborator up to speed

## Rules

- Read, don't skim. The worklog exists to be read.
- Don't read EVERY file — just the most recent and relevant.
- If the worklog is empty (new project), say so and suggest `/kickoff`.
- If CLAUDE.md is unpopulated, say so and suggest `/kickoff`.
- Keep the briefing under 30 lines. Dense, not verbose.
- Never start working on tasks without presenting the catchup first.
  The user needs to confirm the direction.
