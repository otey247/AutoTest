---
name: edge-case-extractor
description: 'Extract edge cases, boundary conditions, and corner cases from requirements or code. Use when finding missing test scenarios, identifying boundary values, discovering error conditions not covered in requirements, or expanding test coverage beyond happy paths.'
---

# Edge Case Extractor

Extract edge cases, boundary conditions, and corner cases from requirements or code to expand test coverage.

## When to Use

- When requirements only describe happy paths
- When identifying boundary values for inputs
- When discovering error conditions not in requirements
- When expanding test scenarios beyond the obvious cases

## Procedure

1. Analyze the requirement, feature, or code for:
   - **Boundary values**: minimum, maximum, just below/above limits
   - **Empty/null inputs**: empty strings, null values, zero quantities
   - **Collection boundaries**: empty list, single item, maximum items
   - **State transitions**: first use, last use, transition between states
   - **Timing issues**: simultaneous requests, timeout, race conditions
   - **Format edge cases**: special characters, unicode, maximum length
   - **Error conditions**: network failure, disk full, permission denied
   - **Business rule edges**: exact threshold values, date boundaries, rounding
2. For each edge case, describe the scenario and expected behavior
3. Categorize by risk level and test priority

## Output Format

```markdown
# Edge Cases for [Feature/Requirement]

## Boundary Values
| Input | Edge Case | Expected Behavior | Priority |
|-------|----------|-------------------|----------|

## Empty/Null Cases
| Scenario | Expected Behavior | Priority |
|----------|-------------------|----------|

## Error Conditions
| Scenario | Expected Behavior | Priority |
|----------|-------------------|----------|

## Timing/Concurrency
| Scenario | Expected Behavior | Priority |
|----------|-------------------|----------|
```
