---
name: failure-log-analyzer
description: 'Analyze test failure logs to extract key failure indicators, error patterns, and root cause clues. Use when investigating test failures, parsing stack traces and error logs, identifying common failure patterns across test runs, or extracting actionable information from verbose test output.'
---

# Failure Log Analyzer

Analyze test failure logs to extract key failure indicators, patterns, and root cause clues.

## When to Use

- When investigating test failures from CI/CD pipelines
- When parsing stack traces and error logs
- When identifying common failure patterns across multiple test runs
- When extracting actionable information from verbose test output

## Procedure

1. Gather the failure logs (test output, stack traces, error logs)
2. Extract key information:
   - **Error message**: The primary error or assertion failure
   - **Stack trace**: Call chain leading to the failure
   - **Failed test**: Which test case failed and at which assertion
   - **Timing**: When the failure occurred (setup, execution, teardown)
   - **Environment**: Any environment-specific indicators
3. Classify the failure:
   - **Assertion failure**: Expected value mismatch
   - **Runtime error**: Exception, crash, or unhandled error
   - **Timeout**: Operation took too long
   - **Connection failure**: Network, database, or service connectivity issue
   - **Configuration error**: Missing config, wrong environment
   - **Resource issue**: Memory, disk, or connection pool exhaustion
4. Identify patterns across multiple failures
5. Suggest likely root cause zone

## Output Format

```markdown
# Failure Log Analysis

## Summary
- Total failures analyzed: [N]
- Failure categories: [list]

## Failures

### Failure 1: [Test Name]
- **Error**: [Error message]
- **Category**: [Assertion / Runtime / Timeout / Connection / Config / Resource]
- **Location**: [File:line or test method]
- **Root cause zone**: [Code / Config / Data / Infra / Dependency]
- **Key indicators**: [What points to the cause]
- **Suggested action**: [What to investigate]

## Patterns
- [Any patterns across failures]
```
