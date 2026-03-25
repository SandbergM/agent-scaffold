---
name: context-budget
description: Context window management. Helps agents stay efficient by choosing what to load, when to delegate, and when to compact. Applied automatically to all commands.
---

## The Problem

Claude's context window is ~200K tokens. That sounds like a lot, but it
fills fast when you're reading files, running tools, and having multi-turn
conversations. When it fills up, compaction kicks in and you lose details.

## Rules of Thumb

### What to Load

| Size | Action |
|------|--------|
| < 100 lines | Read directly, keep in context |
| 100-500 lines | Read directly, but summarize key points for later reference |
| 500-2000 lines | Read sections as needed, not the whole file |
| > 2000 lines | Delegate to a sub-agent, get a summary back |

### When to Delegate vs Read Yourself

**Delegate** when:
- The task produces verbose output (test results, lint reports)
- You need to analyze multiple large files independently
- The work is mechanical (rule checking, doc validation)
- You need a second opinion (Codex review)

**Read yourself** when:
- You need to understand how parts connect (architecture decisions)
- The user is asking YOU, not "some agent"
- The task requires context from the conversation so far
- Files are small enough to hold in working memory

### What to Keep vs Let Go

**Always keep in active context:**
- CLAUDE.md (the project brain)
- The current spec being worked on
- The user's most recent requirements/feedback
- Error messages being debugged

**Load on demand, then summarize:**
- Source files being edited (re-read before each edit)
- Test output (extract pass/fail, discard verbose logs)
- Git diffs (read once, summarize changes)
- Dependency documentation (use Context7 MCP instead of pasting)

**Delegate to sub-agents:**
- Full codebase scans (rule-enforcer, simplifier, doc-enforcer)
- Research tasks (researcher agent)
- Test generation (test-writer, adversarial-tester)
- Security audits (pentester)

### Compaction Awareness

When a session is getting long:

1. **Invoke the summarizer agent** — it compresses your current session
   into a 30-line snapshot saved to worklog. Do this proactively at ~50%
   context usage, not when it's too late.

2. **Write before you forget** — if you've made important decisions or
   found important things, write them to `.claude/worklog/sessions/`
   NOW, not at the end.

3. **Tell the user** — "Session is getting long. I've saved a snapshot.
   Want to continue or start fresh with `/catchup`?"

4. **Fresh session strategy** — if context is getting bloated:
   - Save everything to worklog
   - Suggest: "Let's start a fresh session. Run `/implement` and I'll
     pick up from the worklog."

### Anti-Patterns

- ❌ Reading every source file "just in case" at the start
- ❌ Pasting full test output when only failures matter
- ❌ Keeping old file versions in context after editing
- ❌ Sending the entire codebase to a sub-agent when it only needs 3 files
- ❌ Long conversations without writing to the worklog (risks losing context)

### File Reading Strategy

When exploring a new codebase:

```
1. Read directory structure (ls -R, cheap)
2. Read CLAUDE.md and main entry point
3. Read the specific files relevant to the current task
4. Skim (first 20 lines) of related files
5. Deep-read only when you need to understand implementation details
```

Don't read everything. Read what you need for the current step.

### Sub-Agent Context Budgets

When delegating to sub-agents, be precise about what context they get:

```
Good: "Review app/services/user_service.py and app/models/user_model.py
       for the rules in CLAUDE.md section 'Code Rules'"

Bad:  "Review the codebase"
```

The less context a sub-agent needs, the faster and cheaper it runs, and
the more focused its output will be.
