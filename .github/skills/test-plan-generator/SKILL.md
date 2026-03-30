---
name: test-plan-generator
description: 'Generate structured test plans for features, sprints, or releases. Use when creating test plans, defining test scope and approach, documenting test strategy for a specific feature, or preparing test planning artifacts for team review.'
---

# Test Plan Generator

Generate structured test plans that define scope, approach, resources, schedule, and deliverables.

## When to Use

- When a new feature, sprint, or release needs a test plan
- When documenting test scope and approach for team review
- When preparing for test execution kickoff

## Procedure

1. Gather context: feature scope, requirements, architecture, risks, timeline
2. Define test scope — what is in scope and out of scope
3. Choose test levels and types based on the feature characteristics
4. Define test approach for each level (manual, automated, exploratory)
5. Identify test environment and data requirements
6. Define schedule and milestones
7. Identify risks and mitigation strategies
8. Define deliverables and reporting cadence

## Output Format

```markdown
# Test Plan: [Feature/Sprint/Release Name]

## Scope
- **In scope**: [What will be tested]
- **Out of scope**: [What will not be tested and why]

## Test Approach
| Test Level | Test Types | Approach | Tools |
|-----------|-----------|----------|-------|
| [Unit] | [Functional] | [Automated] | [Jest] |

## Environment and Data
- Environment: [Description]
- Test data: [Strategy]

## Schedule
| Milestone | Target Date | Status |
|-----------|------------|--------|

## Risks and Mitigations
| Risk | Impact | Mitigation |
|------|--------|-----------|

## Deliverables
- [List of test artifacts to produce]
```
