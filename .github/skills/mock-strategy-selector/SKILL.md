---
name: mock-strategy-selector
description: 'Recommend the appropriate mocking strategy for test dependencies. Use when deciding between mocks, stubs, fakes, and spies, when choosing mock libraries, when determining what to mock vs what to use real implementations for, or when test doubles are making tests brittle.'
---

# Mock Strategy Selector

Recommend the appropriate test double strategy (mock, stub, fake, spy) for each dependency.

## When to Use

- When deciding what type of test double to use
- When tests are brittle because of over-mocking
- When determining what to mock vs use real implementations
- When choosing between mock libraries

## Procedure

1. List the dependencies of the code under test
2. For each dependency, evaluate:
   - **Is it fast and deterministic?** → Consider using real implementation
   - **Is it an external service/API?** → Use a stub or fake
   - **Do you need to verify interactions?** → Use a mock or spy
   - **Is it complex with many behaviors?** → Consider a fake
   - **Is it a simple value provider?** → Use a stub
3. Apply these guidelines:
   - **Stub**: Returns canned responses, no interaction verification. Use for queries.
   - **Mock**: Verifies that specific interactions happened. Use for commands and side effects.
   - **Fake**: Working implementation with shortcuts (e.g., in-memory DB). Use when behavior matters but real deps are too heavy.
   - **Spy**: Wraps real implementation, records calls. Use when you want real behavior but need to verify calls.
4. Flag anti-patterns:
   - Mocking what you don't own without an adapter
   - Mocking value objects
   - Over-specifying interaction order
   - Mocking internal implementation details

## Output Format

```markdown
# Mock Strategy for [Component]

| Dependency | Type | Strategy | Rationale |
|-----------|------|----------|-----------|
| [Name] | [DB/API/Service/Lib] | [Mock/Stub/Fake/Spy/Real] | [Why] |

## Anti-patterns to Avoid
- [Any risky mocking decisions and alternatives]
```
