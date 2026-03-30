---
name: branch-coverage-gap-finder
description: 'Identify untested code branches and paths to improve structural coverage. Use when analyzing which branches, conditions, or paths lack test coverage, when improving code coverage metrics, or when finding dead code or unreachable paths.'
---

# Branch Coverage Gap Finder

Identify untested code branches and paths to improve structural test coverage.

## When to Use

- When analyzing which branches lack test coverage
- When improving code coverage metrics
- When finding untested error handling paths
- When identifying dead code or unreachable paths

## Procedure

1. Analyze the code structure:
   - If/else branches
   - Switch/case statements
   - Ternary expressions
   - Try/catch blocks
   - Guard clauses and early returns
   - Loop conditions (zero iterations, one iteration, many iterations)
2. Compare against existing tests to identify:
   - **Untested branches**: Conditions that no test exercises
   - **Partial coverage**: Only true or only false branch tested
   - **Untested error paths**: Catch blocks, error handlers never triggered
   - **Missing loop coverage**: Loop body never executed or always executed once
3. Prioritize gaps by risk:
   - Error handling paths (high risk if untested)
   - Business logic branches (medium-high risk)
   - Validation branches (medium risk)
   - Edge case branches (varies)
4. Recommend specific test scenarios to close gaps

## Output Format

```markdown
# Branch Coverage Gaps

## Summary
- Total branches analyzed: [N]
- Covered branches: [N]
- Uncovered branches: [N]
- Coverage: [X%]

## Uncovered Branches

| Location | Branch | Condition | Risk | Suggested Test |
|----------|--------|-----------|------|----------------|
| [file:line] | [if/else/catch] | [condition] | [High/Med/Low] | [Test scenario] |
```
