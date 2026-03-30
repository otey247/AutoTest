---
name: coverage-gap-analysis
description: 'Identify gaps in test coverage by comparing tested areas against requirements, code changes, and risk profiles. Use when checking for untested requirements, finding uncovered code paths, evaluating whether testing is sufficient for release, or identifying areas that need additional test attention.'
---

# Coverage Gap Analysis

Identify gaps in test coverage by comparing what is tested against what should be tested.

## When to Use

- When checking whether all requirements have test coverage
- When evaluating if testing is sufficient for release
- When code changes need coverage verification
- When risk areas need coverage assessment

## Procedure

1. Define the coverage scope: requirements, code, risk areas, or features
2. Gather existing test inventory (test cases, automated tests, manual test results)
3. Compare tested areas against the coverage scope
4. Identify gaps by category:
   - **Requirement gaps**: Requirements with no tests
   - **Code gaps**: Code paths with no test coverage
   - **Risk gaps**: High-risk areas with insufficient coverage
   - **Type gaps**: Missing test types (e.g., no security tests, no performance tests)
5. Prioritize gaps by risk level
6. Recommend actions to close gaps

## Output Format

```markdown
# Coverage Gap Analysis

## Summary
- Overall coverage: [X%]
- Critical gaps: [N]
- High-priority gaps: [N]

## Gaps by Category

### Requirement Gaps
| Requirement | Risk Level | Gap Description | Recommended Action |
|------------|-----------|----------------|-------------------|

### Code Coverage Gaps
| Module/Component | Current Coverage | Target | Gap |
|-----------------|-----------------|--------|-----|

### Risk Coverage Gaps
| Risk Area | Expected Coverage | Actual Coverage | Priority |
|-----------|------------------|----------------|----------|

## Recommended Actions
1. [Highest priority gap and action]
2. [Next priority]
```
