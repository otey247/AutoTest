---
name: parameterized-test-generator
description: 'Generate parameterized (data-driven) tests that run the same test logic with multiple input/output combinations. Use when a function needs testing with many input variations, when reducing test code duplication, or when creating table-driven tests.'
---

# Parameterized Test Generator

Generate parameterized tests that run the same test logic with multiple input/output combinations.

## When to Use

- When a function needs testing with many input variations
- When reducing duplicate test code
- When creating table-driven or data-driven tests
- When testing equivalence classes systematically

## Procedure

1. Identify the function and its input parameters
2. Define equivalence classes for each parameter:
   - Valid typical values
   - Boundary values
   - Invalid values
   - Special values (null, empty, zero)
3. Create a parameter table covering:
   - At least one value from each equivalence class
   - All boundary values
   - Key combinations for multi-parameter functions
4. Generate parameterized test using the project's framework:
   - Jest: `test.each` or `describe.each`
   - pytest: `@pytest.mark.parametrize`
   - JUnit: `@ParameterizedTest` with `@CsvSource` or `@MethodSource`
   - Go: table-driven tests with test struct slices
5. Include descriptive test case names in the parameter data

## Output Format

Generate parameterized test code with:
- A clear parameter table/data source
- Descriptive name for each case
- Expected results for each parameter combination
- Comments explaining equivalence classes used
