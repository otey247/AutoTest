---
name: assertion-strengthener
description: 'Improve weak or vague test assertions to make tests more meaningful and failure-descriptive. Use when tests have weak assertions like assertTrue or only check for non-null, when test failures produce unhelpful messages, or when tests pass but do not actually validate the right things.'
---

# Assertion Strengthener

Improve weak or vague test assertions to make tests more meaningful and failure messages more descriptive.

## When to Use

- When tests use weak assertions (assertTrue, assertNotNull) that miss real issues
- When test failure messages are unhelpful
- When tests pass but don't actually validate the correct behavior
- When improving test quality during code review

## Procedure

1. Scan test assertions for weakness patterns:
   - **Too weak**: `assertTrue(result != null)` instead of `assertEquals(expected, result)`
   - **Wrong level**: Asserting on serialized strings instead of parsed objects
   - **Missing assertions**: Test runs code but doesn't verify results
   - **Single assertion**: Only checks one property when multiple matter
   - **No error message**: Assertions without descriptive failure messages
2. For each weak assertion, recommend a stronger alternative:
   - Replace boolean checks with specific value assertions
   - Replace string comparisons with structured comparisons
   - Add assertions for additional properties that should be verified
   - Add custom error messages explaining what went wrong
3. Check for missing assertions on side effects (state changes, calls made)

## Output Format

```markdown
# Assertion Improvements

| Location | Current Assertion | Problem | Improved Assertion |
|----------|------------------|---------|-------------------|
| [file:line] | `assertTrue(x > 0)` | Weak: doesn't check exact value | `assertEquals(42, x, "Expected order total to be 42")` |

## Missing Assertions
- [Test name]: Missing assertion on [what should be checked]
```
