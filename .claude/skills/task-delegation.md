---
name: task-delegation
description: Patterns for decomposing work into sub-agent tasks. When to delegate, which agent to use, how to combine results.
---

## When to Delegate

Delegate to a sub-agent when:
- The task is **bounded** — clear input, clear output, doesn't need back-and-forth
- The task produces **verbose output** you don't need in main context
- The task can run on a **cheaper model** (Sonnet vs Opus)
- You need an **independent perspective** (Codex via MCP)

Do NOT delegate when:
- The task requires user interaction (keep in main session)
- The task depends on decisions not yet made
- The result needs deep integration with what you're currently doing

## Agent Selection

| Task | Agent | Why |
|------|-------|-----|
| Write tests for module X | test-writer (Sonnet) | Bounded, formulaic, cheap |
| Try to break module X | adversarial-tester (Sonnet) | Chaos engineering, bounded |
| Review code for bugs | codex-review (Codex MCP) | Independent perspective |
| Check code rules | rule-enforcer (Sonnet) | Mechanical checks, cheap |
| Check complexity | simplifier (Sonnet) | Focused lens, bounded |
| Check docs quality | doc-enforcer (Sonnet) | Focused lens, bounded |
| Review UI/UX quality | ux-reviewer (Sonnet) | Usability + accessibility |
| Review infra/Docker | devops (Sonnet) | Dockerfile, compose, env management |
| Check scope + value | product-manager (Sonnet) | Scope creep, prioritization, YAGNI |
| Security audit | pentester (Opus + Codex) | Needs deep reasoning |
| Research library options | researcher (Sonnet) | Bounded research, cheap |
| Debate architecture | /discuss (Opus + Codex) | Multi-round dialogue |

## Delegation Patterns

### Fan-out: Multiple agents in parallel

When you need multiple independent analyses of the same code:

```
Main agent (Opus):
  ├── codex-review → bugs, logic errors
  ├── simplifier → unnecessary complexity
  ├── rule-enforcer → convention violations
  ├── doc-enforcer → documentation quality
  ├── product-manager → scope creep, acceptance criteria
  ├── pentester → security issues (if auth/input changed)
  ├── devops → infra config issues (if Docker/deploy changed)
  ├── ux-reviewer → usability issues (if UI changed)
  └── adversarial-tester → edge cases and chaos

Combine results → unified review report
```

Use this in `/review` and `/ship`. Each agent gets the same diff but
looks through a different lens.

### Pipeline: Sequential agents

When each agent's output feeds the next:

```
researcher → findings
  ↓
Main agent (Opus) → architecture decision based on research
  ↓
codex-review → validate the decision
  ↓
Main agent → finalize ADR
```

Use this in `/brainstorm` and `/architect`.

### Dialogue: Back-and-forth with Codex

When you need to debate and converge:

```
Claude: "I think we should use approach A because..."
  ↓ (via MCP)
Codex: "I disagree, approach B is better because..."
  ↓
Claude: "Good point about X, but what about Y?"
  ↓ (via MCP)
Codex: "Fair. How about a hybrid: A for this, B for that?"
  ↓
Claude: "Agreed. Let me formalize this."
```

Use this in `/discuss`.

### Builder-Validator: One builds, another checks

```
Main agent (Opus) → writes implementation
  ↓
codex-review (Codex MCP) → reviews for issues
  ↓
Main agent → fixes issues
  ↓
pentester (Opus + Codex) → security review (if auth involved)
  ↓
Main agent → final fixes
```

Use this in `/implement` and `/feature`.

## Context Management

### What to send to sub-agents
- Only the files/code relevant to their task
- The spec section relevant to their review
- CLAUDE.md code rules (for rule-enforcer)
- Clear success criteria

### What NOT to send
- Your entire conversation history
- Unrelated code or context
- Your internal reasoning about other tasks
- Information about other sub-agents' work (unless needed)

### How to combine results
- Deduplicate findings across agents
- Resolve contradictions (e.g., simplifier says "remove this abstraction"
  but codex says "this abstraction prevents a bug")
- Prioritize: security > correctness > simplicity > style
- Present unified findings to user, attributed by source

## Codex MCP Interaction

When calling Codex via MCP, always:

1. **Include full context** — Codex has no memory. Every call is fresh.
   Include relevant code, spec excerpts, and the specific question.

2. **Be specific** — "Review this code" is bad. "Review this auth
   middleware for JWT validation bypass vulnerabilities" is good.

3. **Use sessionId** for multi-round discussions — keeps context between
   calls within the same logical conversation.

4. **Choose the right mode:**
   - `codex_exec` for tasks that need file access
   - Simple prompt for review/analysis tasks

5. **Parse the response** — Codex returns plain text. Extract structured
   findings (severity, location, fix) from its response.
