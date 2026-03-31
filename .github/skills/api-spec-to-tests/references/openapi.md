# OpenAPI / Swagger Test Derivation Patterns

Patterns for systematically deriving test cases from OpenAPI v2 (Swagger) and v3 specifications.

---

## 1. Parsing the Spec

- Resolve all `$ref` references before deriving tests. Inline schemas are easier to reason about.
- Identify the `servers` (v3) or `host`/`basePath` (v2) to build the base URL.
- Extract global `security` definitions — these apply to all operations unless overridden.

---

## 2. Per-Operation Test Derivation

For each `path` + `method` combination, walk through these steps.

### 2.1 Parameters

| Location | What to Test |
|----------|-------------|
| `path` | Valid value, wrong type (string for int), missing (if the framework allows), special characters |
| `query` | Present/absent for required/optional, empty string, array parameters with various `style`/`explode` combos |
| `header` | Required custom headers present/absent, case sensitivity |
| `cookie` | Required cookies present/absent, malformed values |

### 2.2 Request Body

- **Required body**: Send request without body; expect 400.
- **Schema validation**: Send body missing required properties; expect 400 with field-level error.
- **Type coercion**: Send string where integer is expected; verify rejection or coercion per API behavior.
- **Enum fields**: Send a value outside the declared enum; expect 400.
- **Nullable fields**: Send `null` for nullable and non-nullable fields separately.
- **additionalProperties**: If `false`, send an extra unknown field; verify it is rejected or stripped.
- **Nested objects**: Validate required fields at every nesting level, not just the top level.
- **Array items**: Test empty array, single item, boundary-count items, and items with wrong schema.

### 2.3 Response Validation

For every declared response status code:

1. **Status code** — Trigger the condition and assert the code.
2. **Body schema** — Use JSON Schema validation to assert the full body shape.
3. **Headers** — Assert documented response headers (e.g., `Location`, `X-Request-Id`, `Retry-After`).
4. **Content type** — Assert `Content-Type` matches the declared media type.

### 2.4 Security Schemes

| Scheme | Tests |
|--------|-------|
| `apiKey` | Missing key, invalid key, key in wrong location (header vs query) |
| `http` (Bearer) | Missing Authorization header, malformed token, expired token, valid token |
| `http` (Basic) | Missing credentials, wrong password, correct credentials |
| `oauth2` | Missing token, expired token, token with wrong scopes, valid token with correct scopes |
| `openIdConnect` | Same as oauth2, plus discovery document availability |

---

## 3. Schema-Level Patterns

### 3.1 Primitives

| Schema Type | Test Values |
|-------------|------------|
| `integer` | 0, 1, -1, max int, min int, float (should reject) |
| `number` | 0.0, -1.5, very large, very small, NaN if relevant |
| `string` | Empty, single char, max length, special characters, unicode |
| `string` + `format: date` | Valid ISO date, invalid format, leap year edge |
| `string` + `format: email` | Valid, missing @, double @, unicode local part |
| `string` + `format: uuid` | Valid v4, wrong version, truncated |
| `boolean` | `true`, `false`, `0`, `1`, `"true"` (string) |

### 3.2 Constraints

| Constraint | Test |
|-----------|------|
| `minimum` / `maximum` | Value at boundary, one below, one above |
| `minLength` / `maxLength` | String at boundary lengths |
| `pattern` | Matching string, non-matching string |
| `minItems` / `maxItems` | Array at boundary counts |
| `uniqueItems: true` | Array with duplicates — should reject |
| `enum` | Each valid value, one invalid value |
| `required` | Omit each required field individually |
| `readOnly` | Send readOnly field in request — should be ignored or rejected |
| `writeOnly` | Verify writeOnly field is absent from response |

---

## 4. Multi-Response Testing

When an operation declares multiple response codes, create a test scenario that triggers each one:

```
Operation: DELETE /pets/{petId}
  200 → Delete an existing pet (happy path)
  401 → No auth token
  403 → Token without delete permission
  404 → petId that doesn't exist
```

Always verify that the error response body matches its declared schema, not just the status code.

---

## 5. Discriminator and Polymorphism

If the spec uses `oneOf`, `anyOf`, `allOf`, or `discriminator`:

- Test each variant separately.
- Verify the discriminator property selects the correct schema.
- Send a payload that doesn't match any variant; expect 400.
- Send a payload matching multiple variants (for `oneOf`, this should fail).

---

## 6. Spec Completeness Checks

Flag these as gaps during test derivation — they indicate the spec may need updating:

- Operations with no documented error responses (only 200).
- Missing `security` on operations that clearly need auth.
- No `example` or `default` values (makes fixture generation harder).
- `additionalProperties` not explicitly set (ambiguous behavior).
- No `description` on parameters or schemas (reduces test readability).
