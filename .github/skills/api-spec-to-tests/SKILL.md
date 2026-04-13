---
name: api-spec-to-tests
description: 'Derive test cases from API specifications like OpenAPI/Swagger, GraphQL schemas, or gRPC proto files. Use when generating tests from API definitions, validating endpoint behavior against specs, creating API test suites, or verifying request/response contracts.'
---

# API Spec to Tests

Derive test cases from API specifications to validate endpoint behavior against documented contracts.

## When to Use

- When an OpenAPI/Swagger spec needs corresponding tests
- When a GraphQL schema needs query, mutation, and subscription tests
- When gRPC proto files need service-level test coverage
- When validating endpoint behavior against specifications
- When creating comprehensive API test suites from scratch
- When new endpoints or schema changes need test coverage
- When migrating between API versions and verifying backward compatibility

## Procedure — General

1. **Identify the spec format** — Determine whether the input is OpenAPI/Swagger (YAML/JSON), a GraphQL schema (SDL or introspection result), or a gRPC `.proto` file.
2. **Inventory every operation** — List each endpoint, query, mutation, subscription, or RPC method.
3. **Derive test cases per operation** using the categories below.
4. **Validate response structure** against the schema (status code, body shape, headers, error format).
5. **Generate test code** using the project's preferred HTTP/gRPC/GraphQL testing library.
6. **Cross-reference** with security, contract, and input-validation tests (see Related Skills).

### Test Categories to Cover for Every Operation

| Category | What to Test |
|---|---|
| **Happy path** | Valid request with expected 2xx response and correct body schema |
| **Required fields** | Omit each required field one at a time; expect 400/validation error |
| **Type & format** | Send wrong types (string for int, null for non-nullable); verify rejection |
| **Auth/AuthZ** | No token, expired token, wrong role, insufficient scope |
| **Error codes** | Every documented error response (4xx, 5xx) |
| **Content negotiation** | Unsupported Accept/Content-Type headers |
| **Idempotency** | Repeated PUT/DELETE produce the same result |
| **Pagination** | First page, last page, empty results, out-of-range page |
| **Rate limiting** | Verify 429 response and Retry-After header when applicable |
| **Schema validation** | Response body matches the declared schema exactly |

---

## Procedure — OpenAPI / Swagger

Refer to [./references/openapi.md](./references/openapi.md) for detailed patterns.

1. Parse the OpenAPI document (v2 or v3).
2. For each path + method combination:
   a. Read the `parameters`, `requestBody`, and `responses` objects.
   b. Generate one happy-path test using the example values or realistic fixtures.
   c. Generate one negative test per required parameter (omit it, send wrong type).
   d. Generate one test per documented error response code.
   e. If `security` requirements are declared, generate auth tests.
3. Validate response bodies against `$ref` schemas using JSON Schema assertion.
4. Check for `additionalProperties: false` — if set, assert no extra fields in responses.

### Inline Example — OpenAPI Endpoint to Tests

Given this spec fragment:

```yaml
paths:
  /pets/{petId}:
    get:
      operationId: getPetById
      parameters:
        - name: petId
          in: path
          required: true
          schema:
            type: integer
      responses:
        '200':
          description: A pet
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Pet'
        '404':
          description: Pet not found
      security:
        - bearerAuth: []
```

Derive these tests:

```
TEST: GET /pets/{petId} — 200 with valid ID
  → Send GET /pets/1 with valid bearer token
  → Assert status 200
  → Assert body matches Pet schema (id, name, status fields present and typed)

TEST: GET /pets/{petId} — 404 for non-existent ID
  → Send GET /pets/999999 with valid bearer token
  → Assert status 404
  → Assert body contains error message

TEST: GET /pets/{petId} — 400 for invalid ID type
  → Send GET /pets/abc with valid bearer token
  → Assert status 400

TEST: GET /pets/{petId} — 401 without token
  → Send GET /pets/1 with no Authorization header
  → Assert status 401

TEST: GET /pets/{petId} — 403 with insufficient scope
  → Send GET /pets/1 with token lacking required scope
  → Assert status 403
```

See [./examples/petstore-api-tests.md](./examples/petstore-api-tests.md) for a full worked example.

---

## Procedure — GraphQL

Refer to [./references/graphql.md](./references/graphql.md) for detailed patterns.

1. Parse the GraphQL schema (SDL or introspection JSON).
2. For each **Query**:
   a. Generate a test with all required arguments and verify the return type.
   b. Generate a test omitting each required argument; expect a GraphQL error.
   c. Test nullable vs. non-nullable return fields.
   d. Test pagination arguments (`first`, `after`, `last`, `before`) if using Relay-style connections.
3. For each **Mutation**:
   a. Test successful creation/update/delete and verify the returned object.
   b. Test input validation on the `input` type fields.
   c. Test authorization — mutations often need higher privileges than queries.
   d. Verify idempotency where applicable.
4. For each **Subscription** (if present):
   a. Test that subscribing returns data when the triggering event fires.
   b. Test that the subscription payload matches the declared type.
   c. Test unsubscribe behavior.
5. Always test the GraphQL error envelope: verify `errors[].message`, `extensions.code`, and `path`.

---

## Procedure — gRPC

1. Parse the `.proto` file.
2. For each **RPC method**:
   a. Test with a valid request message; verify the response message fields.
   b. Test with missing required fields; expect an `INVALID_ARGUMENT` status.
   c. Test deadline/timeout behavior; expect `DEADLINE_EXCEEDED`.
   d. Test auth metadata (tokens in gRPC metadata headers).
3. For **streaming RPCs**:
   a. Server streaming — verify all messages arrive and the stream completes.
   b. Client streaming — send a sequence and verify the aggregated response.
   c. Bidirectional — verify ordering and completeness.
4. Verify gRPC status codes: `OK`, `NOT_FOUND`, `PERMISSION_DENIED`, `INVALID_ARGUMENT`, `DEADLINE_EXCEEDED`, `UNAVAILABLE`.

---

## HTTP Status Code Testing Matrix

Refer to [./references/status-codes.md](./references/status-codes.md) for the full matrix.

| Code | Trigger Scenario | Verify |
|------|-----------------|--------|
| 200 | Valid request | Correct body schema, headers |
| 201 | Successful POST/PUT creating a resource | Location header, created resource in body |
| 204 | Successful DELETE or update with no body | Empty body |
| 400 | Missing/invalid field, malformed JSON | Error message identifies the field |
| 401 | No credentials or expired token | WWW-Authenticate header |
| 403 | Valid credentials, insufficient permissions | Error body, no data leakage |
| 404 | Non-existent resource ID | Error body, no stack traces |
| 405 | Wrong HTTP method on endpoint | Allow header lists valid methods |
| 409 | Duplicate creation, version conflict | Conflict details in body |
| 415 | Unsupported Content-Type | Accepted types in error or docs |
| 422 | Syntactically valid but semantically wrong | Detailed validation errors |
| 429 | Rate limit exceeded | Retry-After header |
| 500 | Unhandled server error | Generic message, no internals leaked |
| 503 | Service unavailable / maintenance | Retry-After header if applicable |

---

## Common Pitfalls

Avoid these mistakes when deriving tests from API specs:

1. **Testing only the happy path** — A spec with 5 endpoints and only 5 tests is incomplete. Each endpoint needs success, validation, auth, and error tests.
2. **Ignoring schema validation** — Asserting only the status code without verifying the response body matches the declared schema misses breaking changes in field names, types, or structure.
3. **Skipping authentication and authorization tests** — If the spec declares security schemes, test missing, expired, and insufficient-privilege tokens.
4. **Hardcoding test data** — Use parameterized or fixture-based data; hardcoded IDs break across environments.
5. **Not testing error response bodies** — Error responses have schemas too. Verify error codes, messages, and that no internal details (stack traces, SQL) leak.
6. **Forgetting content negotiation** — If the spec supports multiple media types, test each one and test rejection of unsupported types.
7. **Skipping edge cases on parameters** — Empty strings, boundary values, special characters, and extremely long inputs often reveal bugs not covered by happy-path tests.
8. **Treating the spec as the source of truth without questioning it** — Specs can be incomplete or outdated. Flag discrepancies between spec and actual behavior.

---

## Output Format

Generate API test files organized as:

```markdown
# API Tests: [Service / API Name]

**Spec source**: [path or URL to the spec file]
**Generated**: [date]

## Endpoint: [METHOD /path]

### Test: [Short description]
- **Preconditions**: [Auth, test data, state]
- **Request**: [Method, URL, headers, body]
- **Expected response**: [Status code, body schema, headers]
- **Assertions**:
  - Status code is [code]
  - Body matches schema [SchemaName]
  - Header [name] equals [value]

(Repeat for each test case per endpoint)
```

When generating executable test code, follow the project's existing patterns for:
- Test framework (Jest, pytest, Go testing, etc.)
- HTTP client (axios, requests, net/http, etc.)
- Assertion library
- Test data setup and teardown

---

## Related Skills

| Skill | Use When |
|---|---|
| [contract-test-generator](../contract-test-generator/SKILL.md) | Verifying producer-consumer API contracts across teams |
| [input-validation-test-builder](../input-validation-test-builder/SKILL.md) | Deep-diving into injection, overflow, and parser-abuse scenarios for inputs |
| [integration-scenario-builder](../integration-scenario-builder/SKILL.md) | Testing service-to-service interactions beyond individual endpoint tests |
| [schema-diff-impact-analysis](../schema-diff-impact-analysis/SKILL.md) | Assessing test impact when an API schema changes between versions |

---

## Reference Files

- [OpenAPI / Swagger test derivation patterns](./references/openapi.md)
- [GraphQL testing patterns](./references/graphql.md)
- [HTTP status code testing matrix](./references/status-codes.md)
- [Petstore API full worked example](./examples/petstore-api-tests.md)
