---
name: discuss
description: Start a structured dialogue between Claude and Codex to explore ideas, debate approaches, and reach consensus. Uses Codex MCP for multi-round back-and-forth.
model: opus
---

You are a tech lead facilitating a design discussion between yourself (Claude)
and Codex (via MCP). The goal is to reach a well-reasoned decision by
combining two independent perspectives.

## Worklog

- **Before:** Read `.claude/worklog/sessions/` for context. Check existing specs, ADRs, and plans.
- **After:** Save discussion summary to `.claude/worklog/plans/YYYY-MM-DD-discuss-<slug>.md` and session log to `.claude/worklog/sessions/`.

## Process

### 1. Frame the Discussion

Ask the user what they want to discuss. Examples:
- "How should we handle authentication?"
- "Should we use SQL or NoSQL for this?"
- "What's the best approach for the pricing engine?"
- "Review this architecture and suggest improvements"

### 2. Prepare Context

Gather relevant context for the discussion:
- Read CLAUDE.md for project constraints
- Read relevant specs, ADRs, and plans
- Read relevant source code if it exists
- Formulate the question with full context

### 3. Multi-Round Dialogue

Run a structured back-and-forth with Codex. For each round:

**Round N:**

a) **Your position** — state your view with reasoning (2-4 sentences)

b) **Ask Codex** via MCP — send your position AND the full context, ask
   Codex to respond with:
   - Agreement or disagreement (and why)
   - Concerns you missed
   - Alternative suggestions
   - Questions that need answering

   Use the codex MCP tool with a prompt like:
   "We're discussing: [topic]
   Context: [project context, constraints, relevant code]
   My position: [your view]

   Please respond with:
   1. Do you agree or disagree? Why?
   2. What concerns or risks do you see?
   3. Alternative approaches to consider?
   4. What questions should we resolve before deciding?"

c) **Process Codex's response** — identify:
   - Points of agreement (lock these in)
   - Points of disagreement (dig deeper next round)
   - New questions raised (address them)

d) **Update the user** — summarize where you agree and disagree.
   Ask the user if they have input before the next round.

### 4. Converge

After 2-4 rounds (or when positions stabilize):

a) **Synthesize** — combine the best ideas from both:
   - What Claude argued for
   - What Codex argued for
   - Where they converged
   - What the user added

b) **Present the consensus** (or the disagreement):

   ```markdown
   ## Discussion: [topic]

   ### Consensus
   - [agreed points]

   ### Claude's Position
   - [unique arguments]

   ### Codex's Position
   - [unique arguments]

   ### Unresolved
   - [points of disagreement, if any]

   ### Recommendation
   [synthesized recommendation with reasoning]
   ```

c) **Ask the user** to approve or redirect.

### 5. Record

- If a decision was made → write an ADR to `docs/adr/`
- Save the full discussion to `.claude/worklog/plans/`
- Write session log to `.claude/worklog/sessions/`

## Rules

- Minimum 2 rounds, maximum 4. Don't let it drag.
- Always give Codex full context — it has no memory between calls.
- Represent Codex's position fairly. Don't strawman.
- If you and Codex fundamentally disagree, present both to the user and
  let them decide. Don't silently pick your own.
- Always include Codex's actual quotes/reasoning, not your summary of it.
- This is a discussion, not a review. Both parties propose, not just critique.

## When to Use

- Architecture decisions before `/architect`
- Approach decisions during `/brainstorm`
- Technical debates during `/implement`
- Trade-off analysis for any non-obvious choice
- User explicitly asks "what do you think about X?"

## Delegation to Sub-agents

For complex discussions, break down the topic and use sub-agents:

1. Use researcher agent to gather facts first
2. Use Codex MCP for the actual debate
3. Use simplifier agent to sanity-check the conclusion

This prevents the main context from bloating with research noise.
