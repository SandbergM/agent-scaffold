# Spec: {{TITLE}}

**Status:** Draft | Review | Approved
**Author:** {{USER}} + Claude
**Date:** {{DATE}}
**Reviewed by:** Codex (via MCP)
**ADRs:** <!-- link to related ADRs -->

---

## 1. Problem

What problem are we solving? Who has this problem? Why does it matter now?
What happens if we don't solve it?

## 2. Solution Overview

High-level description of what we're building. 3-5 sentences max.

## 3. Non-Goals / Out of Scope

Explicitly list what this project does NOT do. This is as important as goals.

- ❌ 
- ❌ 

## 4. User Stories

| # | As a... | I want to... | So that... | Priority |
|---|---------|-------------|-----------|----------|
| 1 | | | | Must |
| 2 | | | | Must |
| 3 | | | | Should |
| 4 | | | | Could |

## 5. Data Model

```
Entity: name
  - field: type [constraints]
  - field: type [constraints]

Entity → Entity: relationship (1:N, N:M, etc.)
```

## 6. API / Interface Design

For each endpoint, command, or UI flow:

```
ACTION /path
  Input:  { field: type }
  Output: { field: type }
  Errors: 400 (validation), 404 (not found), 500 (internal)
  Auth:   required | public
```

## 7. Edge Cases & Error Handling

| # | Scenario | Input | Expected Behavior | Severity |
|---|----------|-------|-------------------|----------|
| 1 | | | | |
| 2 | | | | |

## 8. Performance Requirements

- **Latency:** p95 < ___ms for ___
- **Throughput:** ___ requests/sec
- **Data volume:** ___ records, ___ growth rate
- **Cold start:** acceptable / not acceptable

## 9. Security

- **Authentication:** How users prove identity
- **Authorization:** Who can do what
- **Data sensitivity:** PII? Financial? Health?
- **Attack surface:** What's exposed, what's hardened
- **Secrets management:** How API keys/passwords are stored

## 10. Dependencies

| Dependency | Type | Risk | Fallback |
|-----------|------|------|----------|
| | External API | API down | |
| | Library | Breaking change | |
| | Infrastructure | Outage | |

## 11. Testing Strategy

- **Unit tests:** What gets unit tested
- **Integration tests:** What boundaries to test
- **E2E tests:** Critical user paths
- **Manual testing:** What can't be automated

## 12. Deployment & Rollback

- How is this deployed?
- How do we know it's working? (monitoring, health checks)
- How do we roll back if it breaks?

## 13. Open Questions

- [ ] 
- [ ] 

## 14. Codex Review Notes

<!-- Filled in by codex-review agent -->

## 15. Sign-off

- [ ] Spec reviewed by Codex
- [ ] All open questions resolved
- [ ] User approved
