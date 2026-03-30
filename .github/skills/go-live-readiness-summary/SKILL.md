---
name: go-live-readiness-summary
description: 'Generate go-live readiness summaries that consolidate all quality evidence for a release decision. Use when making final go/no-go decisions, preparing release approval presentations, consolidating testing and quality evidence for stakeholders, or summarizing residual risk for release reviews.'
---

# Go-Live Readiness Summary

Consolidate all quality evidence into a release readiness summary for go/no-go decisions.

## When to Use

- When making final go/no-go release decisions
- When preparing release approval presentations
- When consolidating quality evidence for stakeholders
- When summarizing residual risk for release reviews

## Procedure

1. Gather evidence from all testing phases:
   - Unit/component test results
   - Integration test results
   - System/E2E test results
   - Performance test results
   - Security test results
   - UAT results
   - Operational acceptance results
2. Summarize quality metrics:
   - Test execution coverage and pass rates
   - Defect status (open, resolved, deferred)
   - Performance metrics vs SLA targets
   - Security findings and their status
3. Assess residual risks:
   - Known issues going into production
   - Mitigations in place for each risk
   - Risk waivers and their conditions
4. Make a recommendation: Go / No-Go / Conditional Go

## Output Format

```markdown
# Go-Live Readiness Summary: [Release Version]

## Recommendation: [GO / NO-GO / CONDITIONAL GO]

## Quality Summary
| Test Phase | Status | Pass Rate | Notes |
|-----------|--------|-----------|-------|
| Unit tests | ✅ | [X%] | |
| Integration tests | ✅ | [X%] | |
| System/E2E tests | ✅ | [X%] | |
| Performance tests | ✅ | Meets SLA | |
| Security tests | ⚠️ | [X findings] | [note] |
| UAT | ✅ | Approved | |

## Open Defects
| ID | Severity | Description | Impact | Mitigation |
|----|----------|-------------|--------|-----------|

## Residual Risks
| Risk | Severity | Mitigation | Waiver |
|------|----------|-----------|--------|

## Conditions (if Conditional Go)
- [Condition 1]
- [Condition 2]

## Approval
| Stakeholder | Decision | Date |
|------------|----------|------|
```
