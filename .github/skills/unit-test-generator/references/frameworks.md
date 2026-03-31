# Framework-Specific Unit Testing Patterns

Detailed guidance for each supported test framework. Use the project's existing conventions first; fall back to these defaults when no precedent exists.

---

## pytest (Python)

### File and Naming Conventions

- Test files: `test_<module>.py` or `<module>_test.py`
- Test functions: `test_<function>_<scenario>_<expected>`
- Test classes (optional): `TestClassName` — group related tests; no inheritance required

### Key Features

**Fixtures** — reusable setup and teardown via dependency injection:

```python
import pytest

@pytest.fixture
def db_connection():
    conn = create_test_connection()
    yield conn
    conn.close()

def test_query_returns_results(db_connection):
    results = db_connection.execute("SELECT 1")
    assert len(results) == 1
```

**Parametrize** — run the same test with multiple inputs:

```python
@pytest.mark.parametrize("input_val, expected", [
    (0, "zero"),
    (1, "one"),
    (-1, "negative"),
])
def test_classify_number(input_val, expected):
    assert classify(input_val) == expected
```

**Exception testing** — verify exceptions with context managers:

```python
def test_divide_by_zero_raises():
    with pytest.raises(ZeroDivisionError):
        divide(10, 0)
```

**Markers** — categorize tests for selective execution:

```python
@pytest.mark.slow
def test_large_dataset_processing():
    ...
```

### Lifecycle Hooks

| Scope | Setup | Teardown |
|-------|-------|----------|
| Function | Fixture with `yield` | Code after `yield` |
| Class | `@pytest.fixture(autouse=True, scope="class")` | Code after `yield` |
| Module | `@pytest.fixture(scope="module")` | Code after `yield` |
| Session | `@pytest.fixture(scope="session")` | Code after `yield` |

### Mocking

Use `unittest.mock` or `pytest-mock` (provides a `mocker` fixture):

```python
def test_sends_email(mocker):
    mock_send = mocker.patch("myapp.email.send")
    notify_user(user)
    mock_send.assert_called_once_with(user.email, subject="Welcome")
```

---

## Jest (TypeScript / JavaScript)

### File and Naming Conventions

- Test files: `<module>.test.ts`, `<module>.spec.ts`, or files in `__tests__/`
- Structure: `describe("module or function")` → `it("does X when Y")`

### Key Features

**Mocking** — built-in mock functions and module mocks:

```typescript
const mockFetch = jest.fn();

describe("fetchUser", () => {
  beforeEach(() => jest.resetAllMocks());

  it("returns user data on success", async () => {
    mockFetch.mockResolvedValue({ id: "1", name: "Alice" });

    const user = await fetchUser("1", mockFetch);

    expect(user).toEqual({ id: "1", name: "Alice" });
    expect(mockFetch).toHaveBeenCalledWith("/users/1");
  });
});
```

**Snapshot testing** — capture and compare output:

```typescript
it("renders the component correctly", () => {
  const tree = render(<Button label="Click" />);
  expect(tree).toMatchSnapshot();
});
```

Use snapshot tests sparingly — they catch unintended changes but can become rubber-stamp approvals if snapshots are too large.

**Async testing** — return promises or use async/await:

```typescript
it("rejects on network error", async () => {
  mockFetch.mockRejectedValue(new Error("timeout"));

  await expect(fetchUser("1", mockFetch)).rejects.toThrow("timeout");
});
```

### Lifecycle Hooks

| Hook | Runs |
|------|------|
| `beforeAll` | Once before all tests in a `describe` block |
| `afterAll` | Once after all tests in a `describe` block |
| `beforeEach` | Before each test |
| `afterEach` | After each test |

### Module Mocking

```typescript
jest.mock("./database", () => ({
  query: jest.fn(),
}));
```

---

## Vitest (TypeScript / JavaScript)

### Key Differences from Jest

Vitest is API-compatible with Jest but uses `vi` instead of `jest`:

```typescript
import { describe, it, expect, vi, beforeEach } from "vitest";

const mockLogger = { info: vi.fn(), error: vi.fn() };

describe("processOrder", () => {
  beforeEach(() => vi.resetAllMocks());

  it("logs success on valid order", () => {
    processOrder(validOrder, mockLogger);
    expect(mockLogger.info).toHaveBeenCalledWith("Order processed");
  });
});
```

- Uses `vi.fn()` instead of `jest.fn()`
- Uses `vi.mock()` instead of `jest.mock()`
- Supports ESM natively without configuration
- Test file patterns are the same as Jest

---

## JUnit 5 (Java)

### File and Naming Conventions

- Test files: `<ClassName>Test.java` in a parallel `src/test/java/` directory
- Methods: `@Test void shouldDoSomething_whenCondition()`

### Key Features

**Assertions** — static imports from `org.junit.jupiter.api.Assertions`:

```java
import static org.junit.jupiter.api.Assertions.*;

@Test
void shouldReturnSum_whenBothPositive() {
    int result = calculator.add(2, 3);
    assertEquals(5, result);
}

@Test
void shouldThrow_whenDivideByZero() {
    assertThrows(ArithmeticException.class, () -> calculator.divide(10, 0));
}
```

**Parameterized tests** — run with multiple inputs:

```java
@ParameterizedTest
@CsvSource({
    "1, 1, 2",
    "0, 0, 0",
    "-1, 1, 0"
})
void shouldAddCorrectly(int a, int b, int expected) {
    assertEquals(expected, calculator.add(a, b));
}
```

**Display names** — human-readable test names in reports:

```java
@Test
@DisplayName("Returns empty list when no users match the filter")
void filterUsers_noMatch() {
    List<User> result = service.filterUsers(users, "nonexistent");
    assertTrue(result.isEmpty());
}
```

### Lifecycle Hooks

| Annotation | Runs |
|-----------|------|
| `@BeforeAll` | Once before all tests (static method) |
| `@AfterAll` | Once after all tests (static method) |
| `@BeforeEach` | Before each test |
| `@AfterEach` | After each test |

### Mocking with Mockito

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    @Mock UserRepository repo;
    @InjectMocks UserService service;

    @Test
    void shouldReturnUser_whenExists() {
        when(repo.findById("u1")).thenReturn(Optional.of(new User("u1", "Alice")));

        User result = service.getUser("u1");

        assertEquals("Alice", result.getName());
        verify(repo).findById("u1");
    }
}
```

---

## xUnit (.NET / C#)

### File and Naming Conventions

- Test files: `<ClassName>Tests.cs` in a separate test project
- Methods: `[Fact]` for single cases, `[Theory]` for parameterized

### Key Features

**Facts and Theories:**

```csharp
public class CalculatorTests
{
    [Fact]
    public void Add_ReturnsSum_WhenBothPositive()
    {
        var calc = new Calculator();
        var result = calc.Add(2, 3);
        Assert.Equal(5, result);
    }

    [Theory]
    [InlineData(1, 1, 2)]
    [InlineData(0, 0, 0)]
    [InlineData(-1, 1, 0)]
    public void Add_ReturnsCorrectSum(int a, int b, int expected)
    {
        var calc = new Calculator();
        Assert.Equal(expected, calc.Add(a, b));
    }
}
```

**Exception testing:**

```csharp
[Fact]
public void Divide_ThrowsDivideByZero_WhenDivisorIsZero()
{
    var calc = new Calculator();
    Assert.Throws<DivideByZeroException>(() => calc.Divide(10, 0));
}
```

### Dependency Injection

xUnit creates a new test class instance per test, so constructor injection replaces `[SetUp]`:

```csharp
public class UserServiceTests
{
    private readonly Mock<IUserRepository> _mockRepo;
    private readonly UserService _service;

    public UserServiceTests()
    {
        _mockRepo = new Mock<IUserRepository>();
        _service = new UserService(_mockRepo.Object);
    }
}
```

### Mocking with Moq

```csharp
[Fact]
public void GetUser_ReturnsUser_WhenExists()
{
    _mockRepo.Setup(r => r.FindById("u1"))
        .Returns(new User("u1", "Alice"));

    var result = _service.GetUser("u1");

    Assert.Equal("Alice", result.Name);
    _mockRepo.Verify(r => r.FindById("u1"), Times.Once);
}
```

---

## Go testing

### File and Naming Conventions

- Test files: `<module>_test.go` in the same package
- Functions: `TestFunctionName_Scenario(t *testing.T)`
- The `_test.go` suffix is required; the Go toolchain recognizes it automatically

### Key Features

**Table-driven tests** — the idiomatic Go testing pattern:

```go
func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"both positive", 2, 3, 5},
        {"both zero", 0, 0, 0},
        {"negative and positive", -1, 1, 0},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := Add(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Add(%d, %d) = %d, want %d", tt.a, tt.b, result, tt.expected)
            }
        })
    }
}
```

**Helper functions** — use `t.Helper()` to get correct line numbers in failure output:

```go
func assertNoError(t *testing.T, err error) {
    t.Helper()
    if err != nil {
        t.Fatalf("unexpected error: %v", err)
    }
}
```

**Subtests** — use `t.Run()` for structured test output:

```go
func TestParse(t *testing.T) {
    t.Run("valid input", func(t *testing.T) {
        result, err := Parse("42")
        assertNoError(t, err)
        if result != 42 {
            t.Errorf("got %d, want 42", result)
        }
    })

    t.Run("invalid input", func(t *testing.T) {
        _, err := Parse("abc")
        if err == nil {
            t.Fatal("expected error for invalid input")
        }
    })
}
```

### Lifecycle

Go tests do not have built-in setup/teardown annotations. Use:

- `TestMain(m *testing.M)` for package-level setup/teardown
- `t.Cleanup(func())` for per-test cleanup (Go 1.14+)

### Mocking

Go relies on interfaces for test doubles. Define an interface, then provide a mock implementation:

```go
type UserRepository interface {
    FindByID(id string) (*User, error)
}

type mockRepo struct {
    user *User
    err  error
}

func (m *mockRepo) FindByID(id string) (*User, error) {
    return m.user, m.err
}
```

---

## Mocha (JavaScript)

### File and Naming Conventions

- Test files: `<module>.test.js`, `<module>.spec.js`, or files in `test/`
- Structure: `describe("module")` → `it("should do X")`

### Key Features

Mocha is a test runner only — pair it with an assertion library (Chai, Node assert, or expect):

```javascript
const { expect } = require("chai");
const { add } = require("../src/math");

describe("add", () => {
  it("should return the sum of two numbers", () => {
    expect(add(2, 3)).to.equal(5);
  });

  it("should handle negative numbers", () => {
    expect(add(-1, 1)).to.equal(0);
  });
});
```

**Async tests** — return a promise or use `async/await`:

```javascript
it("should fetch data", async () => {
  const data = await fetchData("test-id");
  expect(data).to.have.property("id", "test-id");
});
```

### Lifecycle Hooks

| Hook | Runs |
|------|------|
| `before` | Once before all tests in a `describe` block |
| `after` | Once after all tests in a `describe` block |
| `beforeEach` | Before each test |
| `afterEach` | After each test |

### Mocking with Sinon

```javascript
const sinon = require("sinon");

describe("UserService", () => {
  let repoStub;

  beforeEach(() => {
    repoStub = sinon.stub(repo, "findById");
  });

  afterEach(() => sinon.restore());

  it("should return user when found", async () => {
    repoStub.resolves({ id: "u1", name: "Alice" });

    const user = await service.getUser("u1");

    expect(user.name).to.equal("Alice");
    sinon.assert.calledWith(repoStub, "u1");
  });
});
```
