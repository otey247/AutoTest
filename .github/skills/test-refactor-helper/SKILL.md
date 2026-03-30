---
name: test-refactor-helper
description: 'Improve test code quality by identifying and fixing common test smells. Use when tests are slow, brittle, hard to read, duplicated, or tightly coupled to implementation details. Helps with test organization, naming, setup reduction, and maintainability improvements.'
---

# Test Refactor Helper

Identify and fix common test smells to improve test code quality and maintainability.

## When to Use

- When tests are slow, brittle, or hard to maintain
- When test code has excessive duplication
- When tests are tightly coupled to implementation details
- When test organization needs improvement

## Procedure

1. Scan tests for common test smells:
   - **Brittle tests**: Break on implementation changes, not behavior changes
   - **Slow tests**: Unnecessary I/O, heavy setup, unneeded integrations
   - **Duplicate setup**: Same arrange code repeated across many tests
   - **Unclear names**: Test names that don't describe the scenario
   - **Long tests**: Tests doing too many things
   - **Hidden dependencies**: Tests depend on execution order or shared state
   - **Over-mocking**: Too many mocks making tests fragile and unclear
   - **Assertion roulette**: Multiple unrelated assertions in one test
2. For each smell, recommend a specific refactoring:
   - Extract shared setup into fixtures or helpers
   - Rename tests to describe scenario and expected result
   - Split multi-concern tests into focused single-assertion tests
   - Replace implementation mocks with behavior verification
   - Introduce test builders or factories for complex object creation
3. Prioritize by impact on maintainability and reliability

## Output Format

```markdown
# Test Refactoring Recommendations

| Test | Smell | Impact | Recommendation |
|------|-------|--------|---------------|
| [test name] | [smell type] | [High/Med/Low] | [specific refactoring] |

## Quick Wins
- [Easy improvements with high impact]

## Larger Refactorings
- [More involved improvements]
```
