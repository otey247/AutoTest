---
name: unit-test-generator
description: 'Generate unit tests for functions, methods, and classes. Use when creating tests for new code, increasing unit test coverage, testing validation logic, domain rules, error handling, or branch logic. Supports common frameworks like Jest, pytest, JUnit, xUnit, and Go testing.'
---

# Unit Test Generator

Generate unit tests for individual functions, methods, and classes following project conventions.

## When to Use

- When new code needs unit test coverage
- When increasing test coverage for existing code
- When testing validation logic, domain rules, or error handling

## Procedure

1. Analyze the function or class under test:
   - Identify inputs, outputs, and side effects
   - Map branches and conditional logic
   - Identify dependencies that need mocking
2. Detect the project's test framework and conventions by examining existing tests
3. Generate tests for:
   - **Happy path**: Expected inputs produce expected outputs
   - **Negative cases**: Invalid inputs, null/undefined values, empty collections
   - **Boundary values**: Minimum, maximum, zero, one, off-by-one
   - **Error handling**: Exceptions, error codes, error messages
   - **Branch coverage**: Each branch of conditional logic
4. Follow project naming conventions for test files and test names
5. Use descriptive test names that explain the scenario and expected result
6. Keep tests independent — no shared mutable state between tests

## Output Format

Generate test files matching project conventions. Each test should:
- Have a descriptive name: `test_[function]_[scenario]_[expected_result]`
- Arrange: Set up inputs and dependencies
- Act: Call the function under test
- Assert: Verify expected outcomes
- Include comments explaining non-obvious test logic
