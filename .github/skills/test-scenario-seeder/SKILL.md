---
name: test-scenario-seeder
description: 'Derive initial test scenarios from requirements, stories, or acceptance criteria. Use when converting requirements into testable scenarios, seeding test case design from stories, creating initial test scenario inventory, or jumpstarting test design for a new feature.'
---

# Test Scenario Seeder

Derive initial test scenarios from requirements, stories, or acceptance criteria to jumpstart test design.

## When to Use

- When converting requirements into testable scenarios
- When starting test design for a new feature
- When creating an initial test scenario inventory
- When seeding test cases from user stories

## Procedure

1. Parse each requirement or acceptance criterion
2. For each, generate scenarios across these categories:
   - **Happy path**: The expected successful flow
   - **Negative path**: Invalid inputs, unauthorized access, missing data
   - **Boundary cases**: Edge values, limits, thresholds
   - **Error handling**: System errors, timeouts, failures
   - **Alternative flows**: Different valid paths to the same outcome
3. Write each scenario in Given-When-Then format
4. Tag each scenario with test level (unit, integration, e2e) and priority
5. Identify scenarios that need further refinement

## Output Format

```markdown
# Test Scenarios for [Feature/Story]

## Scenario 1: [Descriptive Name]
- **Category**: [Happy path / Negative / Boundary / Error / Alternative]
- **Priority**: [High / Medium / Low]
- **Test Level**: [Unit / Integration / E2E]
- **Given**: [Precondition]
- **When**: [Action]
- **Then**: [Expected result]

## Coverage Summary
| Category | Count |
|----------|-------|
| Happy path | [N] |
| Negative | [N] |
| Boundary | [N] |
| Error handling | [N] |
| Alternative flows | [N] |
```
