---
event: pre-commit
description: Validates staged files against project coding rules before allowing commit.
---

Run the rule-enforcer agent on all staged files. If any rule violations are
found, block the commit and report the violations.

Check these rules on every staged file:
- One class per file
- Max 4 parameters per function
- Max 50 lines per function
- No deep nesting (>3 levels)
- Max 500 lines per file
- All public functions have docstrings
