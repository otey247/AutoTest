# Common Testing Patterns

Reusable patterns that apply across languages and frameworks. Choose the pattern that fits the testing situation.

## Table of Contents

- [Arrange–Act–Assert (AAA)](#arrangeactassert-aaa)
- [Parameterized / Data-Driven Tests](#parameterized--data-driven-tests)
- [Table-Driven Tests (Go Idiom)](#table-driven-tests-go-idiom)
- [Property-Based Testing](#property-based-testing)
- [Test Doubles: Mocks, Stubs, Fakes, and Spies](#test-doubles-mocks-stubs-fakes-and-spies)
- [Builder / Factory Pattern for Test Data](#builder--factory-pattern-for-test-data)

---

## Arrange–Act–Assert (AAA)

The foundational pattern for structuring individual unit tests. Every test follows three phases:

```
Arrange  →  Prepare inputs, create mocks, set up preconditions
Act      →  Call the function or method under test (exactly once)
Assert   →  Verify the expected outcome
```

### Why It Matters

- Makes tests scannable — readers immediately know what's being tested
- Prevents multi-purpose tests that are hard to debug when they fail
- Separates concerns: setup logic doesn't mix with verification logic

### Example (Python)

```python
def test_transfer_reduces_sender_balance():
    # Arrange
    sender = Account(balance=100.0)
    receiver = Account(balance=50.0)

    # Act
    transfer(sender, receiver, amount=30.0)

    # Assert
    assert sender.balance == 70.0
```

### Example (TypeScript)

```typescript
it("reduces sender balance after transfer", () => {
  // Arrange
  const sender = createAccount({ balance: 100 });
  const receiver = createAccount({ balance: 50 });

  // Act
  transfer(sender, receiver, 30);

  // Assert
  expect(sender.balance).toBe(70);
});
```

### Guidelines

- Keep the Act phase to a single function call. If you need multiple calls, consider whether you're testing an integration rather than a unit.
- When multiple assertions verify the same logical outcome (e.g., checking both a return value and a side effect), group them in the Assert phase. If they verify different behaviors, split into separate tests.
- The Arrange phase can be extracted into helper functions or fixtures when setup is repeated across tests.

---

## Parameterized / Data-Driven Tests

Run the same test logic with multiple input/output combinations. This reduces code duplication and makes it easy to add new cases.

### When to Use

- A function has many valid input/output combinations
- You're testing boundary values systematically
- Multiple inputs should produce the same category of result (e.g., all should throw an error)

### Example (pytest)

```python
@pytest.mark.parametrize("input_str, expected", [
    ("hello", "HELLO"),
    ("", ""),
    ("Hello World", "HELLO WORLD"),
    ("123", "123"),
])
def test_to_uppercase(input_str, expected):
    assert to_uppercase(input_str) == expected
```

### Example (JUnit 5)

```java
@ParameterizedTest
@CsvSource({
    "hello, HELLO",
    "'', ''",
    "Hello World, HELLO WORLD",
    "123, 123"
})
void shouldConvertToUppercase(String input, String expected) {
    assertEquals(expected, StringUtils.toUpperCase(input));
}
```

### Example (xUnit)

```csharp
[Theory]
[InlineData("hello", "HELLO")]
[InlineData("", "")]
[InlineData("Hello World", "HELLO WORLD")]
public void ToUppercase_ReturnsExpected(string input, string expected)
{
    Assert.Equal(expected, StringUtils.ToUpperCase(input));
}
```

### Guidelines

- Give each test case a descriptive name or ID when the framework supports it (pytest `ids=`, JUnit `@DisplayName`, Go subtest names)
- Keep the number of parameters per case manageable (3–4 max); beyond that, consider a builder or fixture
- Include at least one boundary case and one error case alongside happy-path cases

---

## Table-Driven Tests (Go Idiom)

The Go community's standard approach to parameterized testing. Define a slice of test cases, then iterate with `t.Run()`.

### Structure

```go
func TestFunction(t *testing.T) {
    tests := []struct {
        name     string
        input    InputType
        expected OutputType
        wantErr  bool
    }{
        // test cases here
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got, err := Function(tt.input)
            if (err != nil) != tt.wantErr {
                t.Fatalf("unexpected error state: %v", err)
            }
            if got != tt.expected {
                t.Errorf("got %v, want %v", got, tt.expected)
            }
        })
    }
}
```

### Full Example

```go
func TestParsePort(t *testing.T) {
    tests := []struct {
        name    string
        input   string
        want    int
        wantErr bool
    }{
        {"valid port", "8080", 8080, false},
        {"min port", "1", 1, false},
        {"max port", "65535", 65535, false},
        {"zero port", "0", 0, true},
        {"negative port", "-1", 0, true},
        {"too large", "65536", 0, true},
        {"non-numeric", "abc", 0, true},
        {"empty string", "", 0, true},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got, err := ParsePort(tt.input)
            if (err != nil) != tt.wantErr {
                t.Fatalf("ParsePort(%q) error = %v, wantErr %v", tt.input, err, tt.wantErr)
            }
            if got != tt.want {
                t.Errorf("ParsePort(%q) = %d, want %d", tt.input, got, tt.want)
            }
        })
    }
}
```

### Guidelines

- Name the struct field `name` and use it in `t.Run()` for clear test output
- Include a `wantErr bool` field to test both success and error cases in the same table
- Keep test case definitions close to the loop so readers see data and logic together

---

## Property-Based Testing

Instead of specifying exact input/output pairs, define properties that must hold for any valid input. The framework generates random inputs to search for counterexamples.

### When to Use

- Functions with a large input space where hand-picked examples might miss edge cases
- Mathematical properties: commutativity, associativity, idempotency, round-trip (encode → decode)
- Parsers, serializers, and formatters where output should be invertible

### Example (Python — Hypothesis)

```python
from hypothesis import given
from hypothesis.strategies import text, integers

@given(text())
def test_encode_decode_roundtrip(s):
    assert decode(encode(s)) == s

@given(integers(), integers())
def test_addition_is_commutative(a, b):
    assert add(a, b) == add(b, a)
```

### Example (TypeScript — fast-check)

```typescript
import fc from "fast-check";

test("encode/decode roundtrip", () => {
  fc.assert(
    fc.property(fc.string(), (s) => {
      expect(decode(encode(s))).toBe(s);
    })
  );
});
```

### Example (Go — rapid)

```go
func TestAddCommutative(t *testing.T) {
    rapid.Check(t, func(t *rapid.T) {
        a := rapid.Int().Draw(t, "a")
        b := rapid.Int().Draw(t, "b")
        if Add(a, b) != Add(b, a) {
            t.Fatalf("Add(%d, %d) != Add(%d, %d)", a, b, b, a)
        }
    })
}
```

### Guidelines

- Property-based tests complement but don't replace example-based tests — use both
- Start with simple properties (roundtrip, idempotency) before writing complex invariants
- When a property test finds a failing case, add that case as an explicit example test to prevent regression

---

## Test Doubles: Mocks, Stubs, Fakes, and Spies

Different types of test doubles serve different purposes. Choose the simplest one that meets your need.

| Type | Purpose | Verifies Calls? | Has Logic? |
|------|---------|-----------------|------------|
| **Stub** | Return canned values for method calls | No | Minimal |
| **Mock** | Verify that specific methods are called with expected arguments | Yes | No |
| **Fake** | Provide a working but simplified implementation (e.g., in-memory DB) | No | Yes |
| **Spy** | Record calls for later inspection without replacing behavior | Yes | Delegates to real |

### When to Use Each

- **Stub**: The test only cares about what the dependency returns, not how it's called
- **Mock**: The test needs to verify that a side effect occurred (e.g., an email was sent)
- **Fake**: The real dependency is too slow or complex, but you need realistic behavior (e.g., an in-memory cache instead of Redis)
- **Spy**: You want to observe calls without changing behavior (useful for logging verification)

### What Not to Mock

- **Pure functions** — they have no side effects; test them directly with inputs and outputs
- **Value objects** — simple data containers; use real instances
- **The code under test itself** — mocking the thing you're testing makes the test meaningless

### Example: Stub vs Mock (TypeScript)

```typescript
// Stub — just returns a value
const stubRepo = { findById: () => Promise.resolve({ id: "1", name: "Alice" }) };

// Mock — tracks and verifies calls
const mockRepo = { findById: jest.fn().mockResolvedValue({ id: "1", name: "Alice" }) };
// Later: expect(mockRepo.findById).toHaveBeenCalledWith("1");
```

---

## Builder / Factory Pattern for Test Data

When test setup is complex, use builder or factory functions to create test objects with sensible defaults.

### Why

- Reduces boilerplate in the Arrange phase
- Makes tests resilient to schema changes (only the factory needs updating)
- Clarifies which fields actually matter for each test

### Example (Python)

```python
def make_user(**overrides):
    defaults = {
        "id": "u1",
        "name": "Alice",
        "email": "alice@example.com",
        "role": "member",
    }
    return User(**(defaults | overrides))

def test_admin_can_delete_posts():
    admin = make_user(role="admin")
    assert admin.can_delete_posts()
```

### Example (TypeScript)

```typescript
function buildUser(overrides: Partial<User> = {}): User {
  return {
    id: "u1",
    name: "Alice",
    email: "alice@example.com",
    role: "member",
    ...overrides,
  };
}

it("allows admin to delete posts", () => {
  const admin = buildUser({ role: "admin" });
  expect(admin.canDeletePosts()).toBe(true);
});
```

### Guidelines

- Provide sensible defaults for every required field so tests only specify what they care about
- Name factory functions clearly: `buildUser`, `makeOrder`, `createTestConfig`
- Avoid sharing mutable objects between tests; create fresh instances each time
