# Coverage Types Guide

A comprehensive reference for the different dimensions of test coverage.

## 1. Requirement Coverage

Measures whether every documented requirement has at least one test verifying it.

| Metric | How to Measure |
|--------|---------------|
| % requirements with tests | Count requirements traced to test cases ÷ total requirements |
| % acceptance criteria covered | Count AC items with corresponding tests ÷ total AC items |

**Indicators of gaps:** Requirements with no linked test case, acceptance criteria not referenced in any test, recently changed requirements with stale tests.

## 2. Code Coverage

Measures which code paths execute during testing.

| Type | What It Measures | Typical Target |
|------|-----------------|---------------|
| Line/Statement | Lines executed | 70–80% |
| Branch | Decision outcomes (if/else) | 60–75% |
| Condition | Individual boolean sub-expressions | 50–70% |
| Path | Unique execution paths through a function | Varies |
| MC/DC | Each condition independently affects the decision | Safety-critical only |

**How to collect:** Use built-in framework reporters — `pytest --cov`, `jest --coverage`, `go test -cover`, `dotnet test --collect:"XPlat Code Coverage"`.

**Watch out for:** High line coverage with low branch coverage often means error-handling paths are untested.

## 3. Risk Coverage

Measures whether high-risk areas have proportionally higher test attention.

| Risk Level | Expected Coverage | Typical Test Types |
|-----------|------------------|-------------------|
| Critical | Comprehensive — unit, integration, e2e, security, performance | All levels |
| High | Strong — unit, integration, key e2e paths | Most levels |
| Medium | Adequate — unit, selective integration | Unit + targeted |
| Low | Basic — unit or smoke | Smoke/unit |

**Indicators of gaps:** Critical modules with only unit tests, high-risk integrations with no contract tests, security-sensitive endpoints with no penetration testing.

## 4. Configuration Coverage

Measures whether the system is tested across the environments and configurations it must support.

- Browser / device combinations
- Operating system versions
- Database versions
- Feature flag combinations
- Locale / timezone settings
- Network conditions (latency, packet loss)

**Indicators of gaps:** Tests only run on one browser, no mobile testing, feature flags tested only in default state.

## 5. Test-Type Coverage

Measures whether the right kinds of testing are applied.

| Test Type | Purpose | Common Gaps |
|-----------|---------|------------|
| Unit | Logic correctness | Error paths, boundary values |
| Integration | Component interaction | Async flows, timeout handling |
| Contract | API compatibility | Schema evolution |
| E2E | Business workflow | Alternative paths, error recovery |
| Performance | Speed and capacity | Spike/soak scenarios |
| Security | Vulnerability prevention | AuthZ, input validation |
| Accessibility | Inclusive design | Keyboard navigation, screen readers |

**Indicators of gaps:** No performance tests for latency-sensitive features, no security tests for auth endpoints, no accessibility tests for public-facing UI.
