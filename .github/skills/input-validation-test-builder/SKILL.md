---
name: input-validation-test-builder
description: 'Build test scenarios for input validation to prevent injection, overflow, parser abuse, and logic bypass. Use when testing form inputs for XSS/SQL injection, validating file uploads, testing API input handling, checking for command injection, or verifying that malicious input is properly sanitized.'
---

# Input Validation Test Builder

Build test scenarios for input validation to prevent injection, overflow, and other input-based attacks.

## When to Use

- When testing form inputs for injection vulnerabilities
- When validating file upload security
- When testing API input handling
- When verifying input sanitization and encoding

## Procedure

1. Identify all input surfaces:
   - Form fields, query parameters, headers, cookies
   - File uploads, API request bodies
   - URL paths, redirect parameters
2. For each input surface, generate test scenarios:
   - **SQL injection**: `' OR '1'='1`, `'; DROP TABLE--`, parameterized vs concatenated
   - **XSS**: `<script>alert(1)</script>`, event handlers, encoded variants
   - **Command injection**: `; ls -la`, `| cat /etc/passwd`, backtick execution
   - **Path traversal**: `../../etc/passwd`, encoded dots and slashes
   - **LDAP injection**: `*)(objectClass=*)`, `)(cn=*`
   - **XML injection**: XXE payloads, entity expansion
   - **Overflow**: Extremely long strings, large numbers, deep nesting
   - **Format string**: `%s%s%s`, `${jndi:ldap://...}`
   - **Null bytes**: `%00` in filenames and parameters
   - **Unicode**: Homograph attacks, right-to-left override
3. Test both client-side and server-side validation
4. Verify that rejected input produces safe error messages (no stack traces or internal details)

## Output Format

```markdown
# Input Validation Tests: [Feature/Endpoint]

## Input Surfaces
| Surface | Type | Current Validation | Risk |
|---------|------|-------------------|------|

## Test Scenarios
| # | Input Surface | Attack Type | Payload | Expected Result | Priority |
|---|-------------|-------------|---------|----------------|----------|
| 1 | Login form | SQL injection | `' OR '1'='1` | Rejected, parameterized query | Critical |

## Validation Checks
- [ ] Server-side validation present (not just client-side)
- [ ] Error messages do not expose internal details
- [ ] All input is sanitized before use
```
