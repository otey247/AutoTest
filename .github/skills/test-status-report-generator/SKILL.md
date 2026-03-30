---
name: test-status-report-generator
description: 'Generate consolidated test status reports for sprints, releases, or stakeholder reviews. Use when preparing test progress summaries, creating dashboards for stakeholders, consolidating results from multiple test streams, or reporting on quality metrics.'
---

# Test Status Report Generator

Generate consolidated test status reports that summarize progress, coverage, findings, and risks.

## When to Use

- When preparing test progress summaries for sprint reviews
- When stakeholders need quality status updates
- When consolidating results from multiple test streams
- When release readiness needs to be communicated

## Procedure

1. Gather test execution data: pass/fail counts, coverage metrics, defect counts
2. Organize by test level or test stream
3. Calculate key metrics: pass rate, coverage percentage, defect density, defect trend
4. Identify blockers, risks, and notable findings
5. Provide a readiness assessment
6. Recommend next actions

## Output Format

```markdown
# Test Status Report — [Date/Sprint/Release]

## Executive Summary
[One-paragraph overall status: on track / at risk / blocked]

## Metrics Dashboard
| Metric | Value | Target | Status |
|--------|-------|--------|--------|
| Test cases executed | [N/M] | [100%] | [On Track / At Risk / Blocked] |
| Pass rate | [X%] | [>95%] | [On Track / At Risk / Blocked] |
| Open defects (Critical/High) | [N] | [0] | [On Track / At Risk / Blocked] |
| Code coverage | [X%] | [>80%] | [On Track / At Risk / Blocked] |

## By Test Stream
| Stream | Planned | Executed | Passed | Failed | Blocked |
|--------|---------|----------|--------|--------|---------|

## Key Findings
- [Notable defects or quality issues]

## Risks and Blockers
- [Current impediments]

## Recommendations
- [Next actions]
```
