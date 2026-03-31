# HTTP Status Code Testing Matrix

A comprehensive matrix mapping HTTP status codes to test scenarios, trigger conditions, and verification points. Use this as a checklist when deriving tests from API specs.

---

## 2xx — Success

| Code | Name | Trigger Scenario | Assertions |
|------|------|-----------------|------------|
| 200 | OK | Valid GET, PUT, or PATCH request | Body matches declared schema; Content-Type correct |
| 201 | Created | Valid POST that creates a resource | `Location` header points to new resource; body contains created entity |
| 202 | Accepted | Async operation initiated | Body contains status/tracking URL; operation is not yet complete |
| 204 | No Content | Successful DELETE, or PUT with no response body | Body is empty; Content-Length is 0 or absent |
| 206 | Partial Content | GET with `Range` header | `Content-Range` header present; body contains only the requested range |

### Success Testing Tips

- Always validate the full response body schema, not just the status code.
- For 201, follow the `Location` header with a GET and verify the resource exists.
- For 204, verify the body is truly empty (not `null` or `{}`).

---

## 3xx — Redirection

| Code | Name | Trigger Scenario | Assertions |
|------|------|-----------------|------------|
| 301 | Moved Permanently | Request deprecated URL | `Location` header points to new URL; clients should update bookmarks |
| 302 | Found | Temporary redirect (e.g., OAuth callback) | `Location` header present; follow redirect and verify destination |
| 304 | Not Modified | GET with `If-None-Match` matching current ETag | No body; ETag header present; cached version is still valid |
| 307 | Temporary Redirect | POST to URL that temporarily moved | `Location` header present; method preserved (POST, not GET) |
| 308 | Permanent Redirect | POST to URL that permanently moved | `Location` header present; method preserved |

### Redirection Testing Tips

- Verify the HTTP client follows (or doesn't follow) redirects as expected by the test.
- For 307/308, verify the request method is preserved (not downgraded to GET).
- For 304, verify the client uses the cached response correctly.

---

## 4xx — Client Errors

| Code | Name | Trigger Scenario | Assertions |
|------|------|-----------------|------------|
| 400 | Bad Request | Malformed JSON, missing required field, wrong data type | Error body identifies the invalid field; no internal details |
| 401 | Unauthorized | Missing or expired authentication credentials | `WWW-Authenticate` header present; no data leakage in body |
| 403 | Forbidden | Valid credentials but insufficient permissions | Error body explains denial reason; no data from the protected resource |
| 404 | Not Found | Request resource with non-existent ID | Error body; no stack traces; consider whether to reveal existence |
| 405 | Method Not Allowed | Use wrong HTTP method (POST on GET-only endpoint) | `Allow` header lists valid methods |
| 406 | Not Acceptable | `Accept` header requests unsupported media type | Error body lists supported types |
| 408 | Request Timeout | Client takes too long to send the full request | Connection closed gracefully |
| 409 | Conflict | Create duplicate resource, or update with stale version (optimistic locking) | Error body identifies the conflict; include current state or version |
| 410 | Gone | Request a permanently deleted resource | Distinguishable from 404; no body required |
| 412 | Precondition Failed | `If-Match` header doesn't match current ETag | Error body; current ETag in response header |
| 413 | Payload Too Large | Send request body exceeding size limit | Error body with size limit; connection may close |
| 415 | Unsupported Media Type | Send `Content-Type` the API doesn't accept | Error body lists accepted types |
| 422 | Unprocessable Entity | Syntactically valid JSON but semantically invalid (e.g., end date before start date) | Detailed validation errors per field |
| 429 | Too Many Requests | Exceed rate limit | `Retry-After` header present; body explains limit; no data processing occurred |

### Client Error Testing Tips

- **400 vs 422**: Use 400 for malformed syntax (bad JSON, wrong type) and 422 for valid syntax but invalid semantics (business rule violation). Test both.
- **401 vs 403**: 401 means "who are you?" (missing/invalid credentials). 403 means "I know who you are, but you can't do this." Test the distinction.
- **404 information leakage**: Returning 404 for resources the user doesn't have access to (instead of 403) prevents enumeration attacks. Verify this behavior.
- **409 with optimistic locking**: Send an update with an old `If-Match` ETag; verify the API returns 409 with the current version.
- **429 retry behavior**: After receiving 429, wait for `Retry-After` duration and retry; verify the request succeeds.

---

## 5xx — Server Errors

| Code | Name | Trigger Scenario | Assertions |
|------|------|-----------------|------------|
| 500 | Internal Server Error | Trigger unhandled exception (may need mocks or fault injection) | Generic error message; no stack traces, SQL, or internal paths leaked |
| 502 | Bad Gateway | Upstream service returns invalid response (reverse proxy scenario) | Error message; verify retry logic if applicable |
| 503 | Service Unavailable | Service in maintenance or overloaded | `Retry-After` header if applicable; verify client retry/backoff behavior |
| 504 | Gateway Timeout | Upstream service doesn't respond in time | Error message; verify timeout value matches configuration |

### Server Error Testing Tips

- **Never expose internals**: Assert that 5xx responses contain a generic message and a correlation/request ID, but never stack traces, SQL queries, file paths, or internal service names.
- **Correlation IDs**: If the API uses request IDs (e.g., `X-Request-Id`), verify they appear in error responses for debugging.
- **Retry safety**: For 502/503/504, document whether the operation is safe to retry. Non-idempotent operations (POST) may not be.
- **Circuit breaker**: If the API implements circuit breaker patterns, test that after N failures, the API returns 503 immediately without calling the failing upstream.

---

## Status Code Decision Flowchart

Use this to determine which status code a test should expect:

```
Request arrives
  → Is it syntactically valid (parseable JSON, correct Content-Type)?
      No  → 400
      Yes → Are credentials provided?
          No  → 401
          Yes → Are credentials valid (not expired, not revoked)?
              No  → 401
              Yes → Does the user have permission for this operation?
                  No  → 403
                  Yes → Does the resource exist?
                      No  → 404
                      Yes → Is the request semantically valid (business rules)?
                          No  → 422
                          Yes → Is there a conflict (duplicate, version mismatch)?
                              No  → Process request → 2xx
                              Yes → 409
```

---

## Test Generation Checklist

For each endpoint in the spec, verify test coverage against this checklist:

- [ ] At least one 2xx happy path test
- [ ] One test per required parameter/field (omitted)
- [ ] One test per documented error code
- [ ] 401 test (if auth is required)
- [ ] 403 test (if roles or scopes are used)
- [ ] 400 test (malformed input)
- [ ] 404 test (non-existent resource)
- [ ] Response body schema validation on every test
- [ ] Response headers validated (Content-Type, Location, etc.)
- [ ] Error response body checked for no information leakage
