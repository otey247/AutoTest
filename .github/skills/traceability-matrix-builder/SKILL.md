---
name: traceability-matrix-builder
description: 'Build traceability matrices that map requirements to test cases, ensuring full coverage and auditability. Use when verifying test coverage against requirements, preparing for audits or compliance reviews, identifying untested requirements, or tracking requirement-to-test-to-result linkage.'
---

# Traceability Matrix Builder

Create traceability matrices that map requirements to test cases for coverage verification and auditability.

## When to Use

- When verifying that all requirements have corresponding tests
- When preparing for audits or compliance reviews
- When identifying gaps in test coverage
- When tracking requirement → test case → test result linkage

## Procedure

1. Gather the list of requirements, stories, or acceptance criteria
2. Gather the list of existing test cases (or planned test scenarios)
3. Create a matrix mapping each requirement to its test cases
4. For each requirement, identify:
   - Number of test cases covering it
   - Test types covering it (unit, integration, e2e, etc.)
   - Coverage gaps (requirements with no tests)
   - Test orphans (tests not linked to any requirement)
5. Assess overall coverage percentage and highlight high-risk gaps
6. Recommend actions for uncovered requirements

## Output Format

```markdown
# Traceability Matrix

## Coverage Summary
- Requirements: [N] total, [N] covered, [N] uncovered
- Coverage: [X]%
- Test orphans: [N] tests not linked to requirements

## Matrix

| Req ID | Requirement | Test Cases | Test Types | Status |
|--------|------------|------------|------------|--------|
| [ID] | [Description] | [TC-1, TC-2] | [Unit, E2E] | Covered |
| [ID] | [Description] | — | — | **GAP** |

## Gaps and Recommendations
- [Uncovered requirement] → [Recommended test approach]
```
