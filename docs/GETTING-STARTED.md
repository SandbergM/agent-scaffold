# Getting Started with agent-scaffold

A step-by-step guide to going from zero to a running project with full AI
agent support.

---

## Prerequisites

Before you start, install these tools:

```bash
# Claude Code — the main engine
npm install -g @anthropic-ai/claude-code

# Codex CLI — independent reviewer (recommended)
npm install -g @openai/codex

# Verify both work
claude --version
codex --version
```

You need API keys for both:

- **Anthropic**: Set `ANTHROPIC_API_KEY` in your environment
- **OpenAI** (for Codex review): Set `OPENAI_API_KEY` or configure in
  `.claude/config.toml`

---

## Step 1: Create Your Project

```bash
# Clone the scaffold directly as your project name
git clone https://github.com/SandbergM/agent-scaffold.git my-awesome-project
cd my-awesome-project

# Reset git history — your project gets a clean start
rm -rf .git
git init

# Run init — creates dirs, checks tools, shows next steps
./init
```

To add the scaffold to an **existing project** instead:

```bash
cd ~/existing-project
git clone https://github.com/SandbergM/agent-scaffold.git /tmp/scaffold
cp -r /tmp/scaffold/.claude /tmp/scaffold/docs /tmp/scaffold/CLAUDE.md /tmp/scaffold/PROGRESS.md .
rm -rf /tmp/scaffold
```

The init script detects whether it's running inside a cloned scaffold
(skips copying) or from an external location (copies files in).

---

## Step 2: Configure MCP Servers

Open `.claude/config.toml` and set up the servers you need.

**Minimum viable setup** — just Codex for reviews:

```toml
[mcp_servers.codex]
transport = "stdio"
command = "npx"
args = ["codex-as-mcp@latest"]
tool_timeout_sec = 600
env = { PROVIDER_API_KEY = "sk-your-openai-key" }
```

**Recommended extras** depending on your project:

| You're building... | Enable these servers |
|--------------------|---------------------|
| Web app | playwright, context7 |
| API with database | postgres or sqlite |
| Google integrations | gws |
| Something with error tracking | sentry |

Uncomment the relevant blocks in `config.toml` and fill in credentials.

---

## Step 3: Kickoff — Define Your Project

```bash
claude /kickoff
```

Claude will interview you about:

- What you're building and for whom
- Tech stack (it'll suggest options with trade-offs)
- Constraints (timeline, budget, team size)
- Architecture principles

Everything gets written into `CLAUDE.md`. This file is the brain of your
project — every agent reads it before doing anything.

**Time:** 5–15 minutes of back-and-forth.

---

## Step 4: Brainstorm (optional but recommended)

```bash
claude /brainstorm
```

If you're not sure how to approach the problem, brainstorm first. Claude
will research prior art, present 2-4 approaches with trade-offs, and
help you pick one. The decision gets recorded as an ADR (Architecture
Decision Record) in `docs/adr/`.

**Skip this if** you already know exactly what you want to build.

---

## Step 5: Grill Me — Lock Down Requirements

```bash
claude /grill-me
```

Or with a brief:

```bash
./grill-me "I want to build a CLI tool that scrapes pricing data from
Swedish e-commerce sites and caches the calculation logic locally"
```

This is the most important step. Claude will:

1. Ask you pointed questions, one at a time
2. Challenge vague answers ("fast" → "what latency?")
3. Cover happy paths, error modes, edge cases, security
4. Draft a complete spec using the 15-section template
5. Send the spec to Codex for independent review
6. Save the approved spec to `docs/specs/`

**Time:** 15–30 minutes. Worth every second.

---

## Step 6: Architect (for non-trivial projects)

```bash
claude /architect
```

Reads your spec and designs:

- Module breakdown with clear responsibilities
- Interface contracts between modules
- Data model
- Build order in phases (each independently testable)
- ADRs for every non-obvious architectural decision

**Skip this if** your project is simple enough that the spec already
implies the architecture (small CLI tools, single-file scripts, etc.)

---

## Step 7: Build

```bash
claude /implement docs/specs/your-spec.md
```

Claude builds module by module, following the architecture:

1. Shows you the implementation plan → you approve
2. Writes interfaces/types first
3. Implements logic
4. Delegates test writing to the test-writer agent (Sonnet, cheap)
5. Runs tests after each module
6. Commits with meaningful messages
7. After each phase: rule-enforcer checks + Codex review

---

## Step 8: Run and Validate

```bash
claude /run
```

Claude builds, starts, and validates the project. If there are issues
(including visual ones for image/UI projects), it iterates automatically
until the output matches the spec.

---

## Step 9: Ship

```bash
claude /ship
```

Runs the full pre-deploy checklist:

- All tests pass
- Code rules satisfied
- Spec compliance verified
- Security checks
- Documentation complete
- Final Codex review
- Ship report generated

---

## Day-to-Day Commands

Once your project is running, these commands handle ongoing work:

| Need to... | Run |
|-----------|-----|
| Add a feature | `claude /feature "description"` |
| Fix a bug | `claude /debug` |
| Refactor messy code | `claude /refactor` |
| Deep audit for bugs | `claude /audit` |
| Review before merge | `claude /review` |
| Build and validate | `claude /run` |

---

## Tips

**Start small.** Your first project with the scaffold should be something
simple. Get a feel for the workflow before tackling your magnum opus.

**Trust the process.** It feels slow to spend 30 minutes on `/grill-me`
before writing code. But every ambiguity caught in spec saves hours of
rework in implementation.

**Customize the skills.** The included skills (`clean-code`, `error-handling`,
`git-conventions`) are starting points. Add your own for domain-specific
patterns.

**Watch your costs.** Opus is powerful but expensive. The scaffold uses
Sonnet for bounded tasks (tests, rule enforcement, research) to keep
costs down. Monitor your API usage for the first few projects.

**Keep CLAUDE.md fresh.** If something changes (new dependency, revised
architecture, dropped feature), update CLAUDE.md. Every agent reads it.
Stale context = bad output.

**Check the worklog.** All agents save their plans, reviews, and session
logs to `docs/worklog/`. If you're wondering "what did we decide about X?"
or "what happened last session?", look there. Every agent reads the last
3-5 session logs before starting work, so they remember what's been done.

---

## Troubleshooting

**Codex review isn't working:**
- Check `claude mcp list` — codex server should appear
- Verify your OpenAI API key is set
- Check `tool_timeout_sec` is high enough (600 = 10 min)

**Claude doesn't know about my project:**
- Make sure CLAUDE.md is populated (run `/kickoff`)
- Check that you're in the project root when running commands

**"Run /kickoff first" but I already did:**
- CLAUDE.md still has placeholder comments? Fill them in or re-run `/kickoff`

**Agents are too expensive:**
- Switch more agents to `model: sonnet` in their YAML frontmatter
- Use `/spec` instead of `/grill-me` for quick projects
- Skip `/brainstorm` and `/architect` for simple projects
