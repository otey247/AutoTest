---
name: unit-test-generator
description: 'Generate unit tests for functions, methods, and classes. Use when writing tests for new code, boosting unit test coverage, testing validation logic, domain rules, error handling, branch logic, or edge cases. Invoke for any task involving creating, scaffolding, or expanding unit tests. Supports Jest, pytest, JUnit, xUnit, Go testing, Vitest, and Mocha.'
---

# Unit Test Generator

Generate unit tests for individual functions, methods, and classes that follow the project's existing conventions, maximize branch coverage, and remain maintainable over time.

## When to Use

- A new function, method, or class needs unit tests
- Existing code has low or missing test coverage
- Validation logic, domain rules, or error handling paths need verification
- A bug fix needs a regression test to prevent recurrence
- A refactor needs safety-net tests before changing implementation
- Conditional or branching logic needs each path exercised

## Procedure

### 1. Discover Project Conventions

Before writing any test code, examine the repository for existing patterns:

- **Find existing test files** — look for files matching `*test*`, `*spec*`, or `__tests__/` directories. Note the naming convention (e.g., `foo.test.ts`, `test_foo.py`, `FooTest.java`).
- **Identify the test framework** — check `package.json` (Jest, Vitest, Mocha), `pyproject.toml` / `setup.cfg` (pytest), `pom.xml` / `build.gradle` (JUnit), `*.csproj` (xUnit/NUnit), or `_test.go` files (Go).
- **Detect assertion style** — does the project use `expect().toBe()`, `assert`, `assertEquals`, `Should()`, or a custom matcher library?
- **Check for test utilities** — look for shared fixtures, factories, builders, or helper functions in test directories. Reuse them instead of duplicating setup logic.
- **Observe file placement** — are tests co-located with source files, or in a parallel `tests/` or `__tests__/` directory?

If no existing tests exist, choose conventions that match the language's community standards. See [./references/frameworks.md](./references/frameworks.md) for framework-specific defaults.

### 2. Analyze the Code Under Test

Read the function, method, or class carefully and identify:

| Aspect | What to Look For |
|--------|-----------------|
| **Inputs** | Parameters, configuration, environment variables, request objects |
| **Outputs** | Return values, mutations to passed-in objects, written files, emitted events |
| **Side effects** | Database calls, HTTP requests, file I/O, logging, cache writes |
| **Branches** | `if/else`, `switch/case`, ternaries, guard clauses, early returns |
| **Error paths** | Thrown exceptions, error return values, validation failures |
| **Dependencies** | Injected services, imported modules, global state |

### 3. Plan Test Cases

For each function or method, generate tests covering these categories (in priority order):

1. **Happy path** — the most common, expected usage with valid inputs
2. **Negative cases** — invalid inputs, null/undefined/nil values, empty collections, wrong types
3. **Boundary values** — min, max, zero, one, empty string, max-length string, off-by-one
4. **Error handling** — exceptions thrown, error codes returned, graceful degradation
5. **Branch coverage** — each conditional path exercised at least once
6. **State transitions** — if the code modifies state, verify before-and-after

Use descriptive test names that encode the scenario and expected outcome. A reader should understand what broke just from the test name, without reading the test body.

### 4. Write the Tests

Follow the **Arrange–Act–Assert** pattern for every test:

```
Arrange  →  Set up inputs, mocks, and preconditions
Act      →  Call the function under test (one call per test)
Assert   →  Verify the outcome (return value, side effects, exceptions)
```

Keep each test focused on a single behavior. If you need multiple assertions, they should all verify the same logical outcome (e.g., checking both a return value and a side effect of one call).

For mocking strategy and when to use mocks vs stubs vs fakes, see the `mock-strategy-selector` skill.

### 5. Verify and Refine

After generating tests:

- Confirm each test can run independently and in any order
- Remove duplicate test scenarios that exercise the same branch
- Add comments only where the test setup or assertion is non-obvious
- Ensure all mocks are reset between tests (use framework-provided lifecycle hooks)

## Inline Examples

### Example A: Python — Testing a Discount Calculator

**Input function:**

```python
def calculate_discount(price: float, customer_type: str) -> float:
    if price <= 0:
        raise ValueError("Price must be positive")
    if customer_type == "premium":
        return round(price * 0.8, 2)
    elif customer_type == "member":
        return round(price * 0.9, 2)
    return price
```

**Generated tests:**

```python
import pytest
from pricing import calculate_discount

class TestCalculateDiscount:
    def test_premium_customer_gets_20_percent_off(self):
        assert calculate_discount(100.0, "premium") == 80.0

    def test_member_customer_gets_10_percent_off(self):
        assert calculate_discount(100.0, "member") == 90.0

    def test_regular_customer_pays_full_price(self):
        assert calculate_discount(100.0, "regular") == 100.0

    def test_zero_price_raises_value_error(self):
        with pytest.raises(ValueError, match="Price must be positive"):
            calculate_discount(0, "premium")

    def test_negative_price_raises_value_error(self):
        with pytest.raises(ValueError, match="Price must be positive"):
            calculate_discount(-10.0, "member")

    def test_discount_rounds_to_two_decimal_places(self):
        assert calculate_discount(33.33, "premium") == 26.66
```

### Example B: TypeScript — Testing a User Lookup Service

**Input function:**

```typescript
export async function getUserById(
  id: string,
  repo: UserRepository
): Promise<User> {
  if (!id || id.trim() === "") {
    throw new Error("User ID is required");
  }
  const user = await repo.findById(id);
  if (!user) {
    throw new NotFoundError(`User ${id} not found`);
  }
  return user;
}
```

**Generated tests:**

```typescript
import { getUserById } from "./userService";
import { NotFoundError } from "./errors";

describe("getUserById", () => {
  const mockRepo = { findById: jest.fn() };

  afterEach(() => jest.resetAllMocks());

  it("returns the user when found", async () => {
    const user = { id: "u1", name: "Alice" };
    mockRepo.findById.mockResolvedValue(user);

    const result = await getUserById("u1", mockRepo as any);

    expect(result).toEqual(user);
    expect(mockRepo.findById).toHaveBeenCalledWith("u1");
  });

  it("throws NotFoundError when user does not exist", async () => {
    mockRepo.findById.mockResolvedValue(null);

    await expect(getUserById("u1", mockRepo as any))
      .rejects.toThrow(NotFoundError);
  });

  it("throws Error when id is empty string", async () => {
    await expect(getUserById("", mockRepo as any))
      .rejects.toThrow("User ID is required");
  });

  it("throws Error when id is only whitespace", async () => {
    await expect(getUserById("   ", mockRepo as any))
      .rejects.toThrow("User ID is required");
  });
});
```

See [./examples/python-pytest.md](./examples/python-pytest.md) and [./examples/typescript-jest.md](./examples/typescript-jest.md) for full worked examples with commentary.

## Output Format

Generate test files matching the project's conventions. Each test should:

- **Name**: Describe the scenario and expected result — `test_[function]_[scenario]_[expected]` (Python), `it("does X when Y")` (JS/TS), `Test_Function_Scenario_Expected` (Go)
- **Structure**: Follow Arrange–Act–Assert (see [./references/patterns.md](./references/patterns.md))
- **Assertions**: Use specific matchers (`toEqual`, `pytest.raises`, `assert.Equal`) over generic truthy checks
- **Independence**: No test should depend on another test's execution or ordering

## Common Pitfalls

Avoid these anti-patterns when generating unit tests:

| Pitfall | Why It Hurts | What to Do Instead |
|---------|-------------|-------------------|
| **Testing implementation, not behavior** | Tests break on every refactor even when behavior is unchanged | Assert on outputs and observable side effects, not internal method calls |
| **One giant test per function** | Failure messages are vague; hard to pinpoint which scenario broke | Write one test per scenario with a descriptive name |
| **Over-mocking** | Tests pass but production breaks because mocks hide real bugs | Only mock external dependencies (I/O, network, time); keep pure logic unmocked |
| **Hardcoded magic values** | Unclear what the value represents; easy to copy-paste errors | Use named constants or variables that document intent: `const MAX_RETRIES = 3` |
| **Shared mutable state** | Tests pass individually but fail when run together or in different order | Use fresh setup in each test via `beforeEach` / `setUp` / test-local variables |
| **Missing error-path tests** | Error handling code ships untested; bugs hide in catch blocks | Test every exception, error return, and validation rejection |
| **Asserting too little** | Tests pass but don't actually verify the right thing (e.g., `assert result is not None`) | Use precise assertions: check exact values, error messages, call arguments |
| **Copy-pasting tests** | Duplicated tests rot when requirements change; only one copy gets updated | Extract common setup into helpers; use parameterized tests for input variations |

## Framework-Specific Guidance

Each framework has idiomatic conventions. Follow the project's existing patterns first; fall back to these defaults:

| Framework | Test File Pattern | Naming Convention | Key Idiom |
|-----------|------------------|-------------------|-----------|
| **pytest** | `test_*.py` | `test_function_scenario_expected` | Fixtures, `parametrize`, context managers |
| **Jest** | `*.test.ts` / `*.spec.ts` | `describe` + `it("does X when Y")` | `beforeEach`, `jest.fn()`, `toEqual` |
| **Vitest** | `*.test.ts` / `*.spec.ts` | Same as Jest | API-compatible with Jest; uses `vi.fn()` |
| **JUnit 5** | `*Test.java` | `@Test void shouldDoX_whenY()` | `@BeforeEach`, `assertThrows`, `@ParameterizedTest` |
| **xUnit** | `*Tests.cs` | `[Fact]` / `[Theory]` | Constructor injection, `Assert.Equal`, `Assert.Throws` |
| **Go testing** | `*_test.go` | `TestFunction_Scenario` | Table-driven tests, `t.Run`, `t.Helper()` |
| **Mocha** | `*.test.js` / `*.spec.js` | `describe` + `it("should do X")` | `before`, `after`, Chai or assert |

For detailed patterns, lifecycle hooks, and idiomatic examples for each framework, see [./references/frameworks.md](./references/frameworks.md).

## Testing Patterns Reference

Reusable patterns that apply across frameworks:

- **Arrange–Act–Assert (AAA)** — standard structure for readable tests
- **Parameterized / table-driven tests** — reduce duplication for input variations
- **Property-based testing** — generate random inputs to find edge cases automatically
- **Test doubles** — mocks, stubs, fakes, and spies for isolating dependencies

See [./references/patterns.md](./references/patterns.md) for detailed guidance on each pattern.

## Related Skills

These skills complement `unit-test-generator` for broader testing coverage:

| Skill | When to Use Together |
|-------|---------------------|
| `parameterized-test-generator` | When a function needs testing with many input/output combinations |
| `mock-strategy-selector` | When deciding what to mock, stub, or fake in unit tests |
| `assertion-strengthener` | When existing tests have weak assertions like `assertTrue` or null checks |
| `branch-coverage-gap-finder` | When you need to identify which code branches still lack test coverage |
| `edge-case-extractor` | When you want to discover boundary conditions and corner cases from requirements |
| `component-test-generator` | When testing needs to go beyond a single unit to cover a feature slice |
| `boundary-value-test-builder` | When inputs have defined ranges and you need systematic boundary tests |
| `test-refactor-helper` | When existing tests are brittle, duplicated, or hard to maintain |
| `coverage-gap-analysis` | When evaluating whether overall test coverage is sufficient for release |
