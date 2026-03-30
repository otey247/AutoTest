---
name: release-readiness-checklist
description: 'Generate release readiness checklists that verify all quality gates are met before deployment. Use when preparing for a release, verifying exit criteria, checking that all testing phases are complete, or making go/no-go decisions.'
---

# Release Readiness Checklist

Generate comprehensive release readiness checklists that verify all quality gates before deployment.

## When to Use

- When preparing for a production release
- When verifying that all exit criteria are met
- When making go/no-go release decisions
- When documenting release quality evidence

## Procedure

1. Verify testing completion across all required phases
2. Check defect status — no critical or high open defects
3. Verify performance and security test results
4. Confirm operational readiness (monitoring, alerting, rollback)
5. Validate deployment procedure and rollback plan
6. Collect stakeholder sign-offs

## Output Format

```markdown
# Release Readiness Checklist — [Release Name/Version]

## Testing Completion
- [ ] Unit tests passing (coverage: [X%])
- [ ] Integration tests passing
- [ ] E2E/system tests passing
- [ ] Performance tests passing (meets SLA targets)
- [ ] Security tests passing (no critical findings)
- [ ] UAT completed and signed off
- [ ] Regression suite passing

## Defect Status
- [ ] No open S1 (Critical) defects
- [ ] No open S2 (High) defects without approved waivers
- [ ] All deferred defects documented with risk acceptance

## Operational Readiness
- [ ] Monitoring and alerting configured
- [ ] Rollback procedure documented and tested
- [ ] Deployment runbook reviewed
- [ ] On-call support briefed

## Sign-offs
| Role | Name | Status | Date |
|------|------|--------|------|

## Go/No-Go Recommendation
[Recommendation with supporting evidence]
```
