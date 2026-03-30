---
name: api-spec-to-tests
description: 'Derive test cases from API specifications like OpenAPI/Swagger, GraphQL schemas, or gRPC proto files. Use when generating tests from API definitions, validating endpoint behavior against specs, creating API test suites, or verifying request/response contracts.'
---

# API Spec to Tests

Derive test cases from API specifications to validate endpoint behavior against documented contracts.

## When to Use

- When an OpenAPI/Swagger spec needs corresponding tests
- When validating endpoint behavior against specifications
- When creating comprehensive API test suites
- When new endpoints need test coverage

## Procedure

1. Parse the API specification (OpenAPI, GraphQL schema, gRPC proto)
2. For each endpoint/operation, generate tests covering:
   - **Success cases**: Valid requests with expected responses
   - **Validation errors**: Missing required fields, wrong types, invalid formats
   - **Authentication/Authorization**: Unauthenticated, unauthorized, forbidden
   - **Error responses**: All documented error codes
   - **Content negotiation**: Supported content types
   - **Idempotency**: Repeated calls produce consistent results (for PUT/DELETE)
   - **Pagination**: First page, last page, empty results (if applicable)
3. Verify response structure matches the schema (status code, body shape, headers)
4. Generate test code using the project's preferred HTTP testing approach

## Output Format

Generate API test files that:
- Cover all endpoints in the spec
- Test success, validation, auth, and error paths
- Validate response status codes, body schemas, and headers
- Include setup/teardown for test data
- Follow project conventions for API testing
