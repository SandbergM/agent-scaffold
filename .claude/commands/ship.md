---
name: ship
description: Pre-deploy validation checklist. Runs all quality gates, verifies spec compliance, and prepares for deployment.
model: opus
---

You are a release engineer doing final validation before deploy.

## Worklog

- **Before:** Read `.claude/worklog/reviews/` for any unresolved findings. Read `.claude/worklog/sessions/` to verify all planned work is complete. Check `.claude/worklog/checklists/` for any prior ship attempts.
- **After:** Save ship checklist to `.claude/worklog/checklists/YYYY-MM-DD-ship-<slug>.md` and session log to `.claude/worklog/sessions/`.

## Checklist

Run through ALL of these. Report pass/fail for each.

### 1. Tests
- [ ] All tests pass: `<test command from CLAUDE.md>`
- [ ] No skipped or disabled tests without documented reason
- [ ] Coverage is acceptable (check if coverage tool is configured)

### 2. Code Quality
- [ ] Run rule-enforcer agent on entire `src/`
- [ ] No files over 500 lines
- [ ] No functions over 50 lines
- [ ] No functions with >4 params without DTOs
- [ ] All public functions have docstrings

### 3. Spec Compliance
- [ ] Read the spec from `docs/specs/`
- [ ] Verify every requirement is implemented
- [ ] Verify every edge case from the spec is handled
- [ ] List any deviations and get user approval

### 4. Security
- [ ] No secrets in code (grep for API keys, passwords, tokens)
- [ ] No hardcoded URLs for production services
- [ ] Dependencies are pinned to specific versions
- [ ] Input validation on all external inputs

### 5. Documentation
- [ ] README is up to date
- [ ] CLAUDE.md reflects current state
- [ ] ADRs exist for all major decisions
- [ ] API documentation (if applicable) matches implementation

### 6. Git Hygiene
- [ ] Clean git status (no uncommitted changes)
- [ ] Meaningful commit messages
- [ ] No merge conflicts
- [ ] Branch is up to date with main

### 7. Infrastructure (if applicable)
- [ ] Run devops agent on Dockerfile, docker-compose, env config
- [ ] Health check endpoint works
- [ ] .env.example matches all required variables
- [ ] No secrets in Docker images or compose files
- [ ] Resource limits set on containers

### 8. Scope Validation
- [ ] Run product-manager agent — all acceptance criteria met
- [ ] No scope creep (implementation matches spec)
- [ ] All must-have stories complete
- [ ] Nice-to-haves documented in backlog, not snuck in

### 9. Codex Final Review
- [ ] Send full diff (main..HEAD) to codex-review agent
- [ ] All blockers resolved
- [ ] Warnings acknowledged

## Output

Generate a ship report:
```
# Ship Report — <project> — <date>

## Status: ✅ READY / ❌ BLOCKED

### Results
✅ Tests: 47 passed, 0 failed
✅ Code quality: all rules pass
⚠️  Spec compliance: 1 deviation (documented)
...

### Blockers (if any)
- ...

### Sign-off
Ready to merge/deploy.
```

Save the report to `.claude/worklog/checklists/YYYY-MM-DD-ship-report.md`.

## CI/CD Generation

After the checklist passes, generate a CI/CD pipeline:

1. **Read CLAUDE.md** for tech stack and test commands.
2. **Generate `.github/workflows/ci.yml`** based on what the project uses:

   - **Python**: install deps, run pytest, run linter, run type checker
   - **Node/TS**: install deps, build, run tests, run lint
   - **Docker**: build image, run tests in container
   - **FastAPI**: install, run pytest with coverage, run mypy

3. **Include these steps:**
   - Checkout code
   - Set up language runtime (correct version from CLAUDE.md)
   - Install dependencies (cached)
   - Run linter/formatter check
   - Run type checker (if applicable)
   - Run test suite
   - Run coverage (if configured)
   - Build step (if applicable)

4. **Ask the user** before writing the file. Show the workflow and confirm.

Example output:
```yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - run: pip install -r requirements.txt
      - run: pytest --cov=app tests/
      - run: mypy app/
```
Update PROGRESS.md to reflect ship status.
