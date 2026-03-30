---
name: flaky-test-detector
description: 'Identify flaky tests that pass and fail intermittently without code changes. Use when tests fail inconsistently across CI runs, when investigating non-deterministic test behavior, when deciding which tests to quarantine, or when analyzing test reliability metrics.'
---

# Flaky Test Detector

Identify flaky tests that pass and fail intermittently to separate real defects from test infrastructure issues.

## When to Use

- When tests fail inconsistently across CI runs
- When investigating non-deterministic test behavior
- When deciding which tests to quarantine
- When analyzing test reliability metrics

## Procedure

1. Identify candidate flaky tests using these indicators:
   - **Inconsistent results**: Same test passes and fails on same code
   - **Timing sensitivity**: Failures correlate with load or time of day
   - **Order dependency**: Fails only when run after specific tests
   - **Environment sensitivity**: Fails only in CI, not locally
   - **Resource sensitivity**: Fails under resource pressure
2. Analyze common flaky patterns:
   - **Race conditions**: Async operations without proper waits
   - **Shared state**: Tests modify shared data without cleanup
   - **Time dependencies**: Tests depend on current time or timezone
   - **Network calls**: Tests call real external services
   - **Random data**: Tests use random inputs without seeding
   - **File system**: Tests depend on file system state
   - **Port conflicts**: Tests bind to specific ports
3. Recommend remediation or quarantine:
   - Fix: Add proper waits, isolate state, mock externals
   - Quarantine: Move to separate suite, don't block pipeline
   - Delete: If the test provides no value

## Output Format

```markdown
# Flaky Test Analysis

## Identified Flaky Tests

| Test | Failure Rate | Pattern | Root Cause | Recommendation |
|------|-------------|---------|-----------|---------------|
| [test name] | [X%] | [race/state/time/network] | [likely cause] | [Fix/Quarantine/Delete] |

## Quick Fixes
- [Tests that can be fixed immediately]

## Quarantine Candidates
- [Tests to move to non-blocking suite]
```
