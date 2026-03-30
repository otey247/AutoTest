---
name: regression-pack-curator
description: 'Curate and maintain regression test suites by selecting, prioritizing, and pruning tests. Use when updating regression suites after feature changes, selecting the right subset of tests for release regression, identifying obsolete or redundant tests, or optimizing regression execution time while maintaining coverage.'
---

# Regression Pack Curator

Curate and maintain regression test suites by selecting, prioritizing, and pruning tests.

## When to Use

- When updating regression suites after feature changes
- When selecting regression tests for a release
- When identifying obsolete or redundant tests to remove
- When optimizing regression execution time

## Procedure

1. Assess the current regression suite:
   - Total tests and execution time
   - Coverage by feature area
   - Test age and last modification date
   - Pass/fail history and flakiness
2. Based on recent changes, determine:
   - **Tests to add**: New scenarios for changed functionality
   - **Tests to update**: Existing tests affected by changes
   - **Tests to remove**: Obsolete tests for removed features
   - **Tests to prioritize**: High-risk areas get earlier execution
3. Apply curation rules:
   - Remove duplicate coverage (tests that verify the same thing)
   - Flag flaky tests for investigation or quarantine
   - Prioritize by risk: critical paths first, then high-risk changes, then breadth
   - Consider execution time — keep the suite efficient
4. Produce a curated regression pack with execution order

## Output Format

```markdown
# Regression Pack: [Release/Sprint]

## Suite Health
- Total tests: [N] (was [N])
- Estimated execution time: [duration]
- Flaky tests quarantined: [N]

## Changes
| Action | Test | Reason |
|--------|------|--------|
| Add | [test name] | [why] |
| Update | [test name] | [why] |
| Remove | [test name] | [why] |
| Quarantine | [test name] | [flaky/obsolete] |

## Execution Order
1. [Critical path tests]
2. [High-risk area tests]
3. [Breadth coverage tests]
```
