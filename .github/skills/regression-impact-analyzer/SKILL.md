---
name: regression-impact-analyzer
description: 'Analyze the regression impact of code changes to determine which existing tests need to be re-run. Use when assessing which tests are affected by a code change, determining the regression blast radius, selecting regression test subsets for CI, or evaluating change risk based on code dependencies.'
---

# Regression Impact Analyzer

Analyze the regression impact of code changes to determine which tests need re-execution.

## When to Use

- When assessing which tests are affected by code changes
- When determining the regression blast radius of a change
- When selecting regression test subsets for CI
- When evaluating change risk based on code dependencies

## Procedure

1. Identify the changed files and functions
2. Trace the impact outward:
   - **Direct dependents**: Code that directly calls/uses the changed code
   - **Indirect dependents**: Code that depends on direct dependents
   - **Shared resources**: Data, configuration, or state used by the change
   - **Integration points**: APIs, contracts, or interfaces affected
3. Map impact to test suites:
   - Which unit tests cover the changed code?
   - Which integration tests exercise the affected interfaces?
   - Which e2e tests traverse the changed paths?
4. Categorize by impact confidence:
   - **Definite**: Tests that directly exercise changed code
   - **Likely**: Tests that exercise dependent code
   - **Possible**: Tests that share resources or integration points
5. Recommend regression scope based on risk tolerance

## Output Format

```markdown
# Regression Impact Analysis: [Change Description]

## Changed Files
| File | Functions Changed | Direct Dependents |
|------|------------------|-------------------|

## Impact Map
| Impacted Area | Impact Type | Confidence | Affected Tests |
|--------------|------------|-----------|---------------|
| [area] | [Direct/Indirect/Shared] | [Definite/Likely/Possible] | [test names] |

## Recommended Regression Scope
### Minimum (Definite impact only)
- [Tests that must run]
- Estimated time: [X minutes]

### Standard (Definite + Likely)
- [Additional tests]
- Estimated time: [X minutes]

### Full (All impacted)
- [All affected tests]
- Estimated time: [X minutes]
```
