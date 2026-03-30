---
name: authn-test-builder
description: 'Build test scenarios for authentication flows including login, MFA, password reset, session establishment, and token management. Use when testing login security, validating MFA implementation, checking password policies, testing session creation, or verifying token-based authentication.'
---

# Authentication Test Builder

Build test scenarios for authentication flows covering login, MFA, password management, and session establishment.

## When to Use

- When testing login security and credential handling
- When validating MFA implementation
- When checking password policy enforcement
- When testing session establishment and token management
- When verifying authentication for APIs

## Procedure

1. Identify authentication mechanisms in use (password, MFA, OAuth, API key, certificate)
2. Generate test scenarios for each mechanism:
   - **Valid credentials**: Successful authentication
   - **Invalid credentials**: Wrong password, unknown user
   - **Brute force protection**: Account lockout after N failures
   - **Password policy**: Minimum length, complexity, history
   - **Password reset**: Token generation, expiration, single-use
   - **MFA**: Enrollment, verification, backup codes, bypass attempts
   - **Session management**: Creation, timeout, invalidation on logout
   - **Token handling**: Expiration, refresh, revocation
   - **Account enumeration**: Login error messages don't reveal valid accounts
3. Include OWASP authentication testing checks
4. Prioritize by exploitability

## Output Format

```markdown
# Authentication Tests: [System]

## Test Scenarios

| # | Category | Scenario | Expected Result | OWASP Ref | Priority |
|---|----------|----------|----------------|-----------|----------|
| 1 | Login | Valid credentials | Authentication succeeds | 4.4.1 | High |
| 2 | Login | Invalid password | Generic error, no account info leaked | 4.4.1 | High |
| 3 | Brute force | 10 failed attempts | Account locked for [X] minutes | 4.4.3 | Critical |

## Automated Checks
- [ ] [Check that can be automated]

## Manual Checks
- [ ] [Check requiring manual verification]
```
