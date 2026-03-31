# GraphQL Testing Patterns

Patterns for deriving test cases from GraphQL schemas covering queries, mutations, subscriptions, and error handling.

---

## 1. Schema Analysis

Before writing tests, inventory the schema:

- List all **Query** fields and their argument types.
- List all **Mutation** fields and their input types.
- List all **Subscription** fields and their trigger conditions.
- Identify custom **scalar** types and their validation rules.
- Map **enum** types and their valid values.
- Note **interface** and **union** types for polymorphic response testing.
- Check for **directives** like `@deprecated`, `@auth`, `@cacheControl`.

---

## 2. Query Testing

### 2.1 Basic Query Tests

For each query field:

```graphql
# Example schema
type Query {
  user(id: ID!): User
  users(filter: UserFilter, first: Int, after: String): UserConnection!
}
```

Derive these test categories:

| Test | What to Do |
|------|-----------|
| **Happy path** | Send valid arguments; assert return type matches schema |
| **Required argument missing** | Omit `id`; expect GraphQL validation error |
| **Invalid argument type** | Send string where Int expected; expect error |
| **Null return** | Query with ID that doesn't exist; assert `null` for nullable field or error for non-null |
| **Partial field selection** | Request only a subset of fields; verify only requested fields returned |
| **Nested resolution** | Request nested objects (e.g., `user { posts { title } }`); verify resolver chain |
| **Aliases** | Use aliases to query the same field twice with different arguments |

### 2.2 Pagination (Relay Connection Pattern)

If the schema uses Relay-style connections:

```graphql
type UserConnection {
  edges: [UserEdge!]!
  pageInfo: PageInfo!
}
```

Test:

- `first: N` returns at most N edges.
- `after: cursor` skips to the correct position.
- `pageInfo.hasNextPage` is true when more results exist, false on the last page.
- Empty result set returns empty `edges` array and `hasNextPage: false`.
- Invalid cursor returns an error or empty result (depends on implementation).

### 2.3 Filtering and Sorting

- Apply each filter field individually; verify filtered results.
- Combine multiple filters; verify AND/OR behavior.
- Apply sort in ascending and descending order; verify order.
- Filter that matches nothing returns empty, not an error.

---

## 3. Mutation Testing

### 3.1 Create Mutations

```graphql
type Mutation {
  createUser(input: CreateUserInput!): User!
}

input CreateUserInput {
  name: String!
  email: String!
  role: Role = VIEWER
}
```

| Test | What to Do |
|------|-----------|
| **Happy path** | Valid input; assert created object returned with correct fields |
| **Missing required field** | Omit `name`; expect validation error |
| **Invalid field type** | Send integer for `email`; expect error |
| **Invalid enum value** | Send `role: SUPERADMIN`; expect error |
| **Default value** | Omit `role`; verify default `VIEWER` is applied |
| **Duplicate** | Create the same entity twice; verify conflict handling |
| **Authorization** | Call without auth token; expect authentication error |
| **Insufficient role** | Call with viewer token when admin is required; expect authorization error |

### 3.2 Update Mutations

- Update each field individually; verify only that field changed.
- Send unchanged values; verify no side effects.
- Update non-existent entity; expect NOT_FOUND error.
- Test partial updates vs. full replacement semantics.

### 3.3 Delete Mutations

- Delete existing entity; verify it is removed.
- Delete non-existent entity; expect NOT_FOUND or idempotent success.
- Delete with insufficient permissions; expect FORBIDDEN.
- Verify cascade effects (related entities deleted or orphaned as expected).

---

## 4. Subscription Testing

### 4.1 Basic Subscription Flow

```graphql
type Subscription {
  userCreated: User!
}
```

1. Establish WebSocket connection and subscribe.
2. Trigger the event (create a user via mutation).
3. Assert the subscription message matches the declared type.
4. Unsubscribe; trigger event again; verify no message received.

### 4.2 Subscription Edge Cases

| Test | What to Do |
|------|-----------|
| **Authorization** | Subscribe without token; expect connection rejected |
| **Invalid subscription** | Subscribe to non-existent field; expect error |
| **Multiple subscribers** | Two clients subscribe; both receive the event |
| **Filtered subscriptions** | Subscribe with filter args; verify only matching events arrive |
| **Connection drop** | Simulate disconnect; verify reconnection behavior |

---

## 5. Error Handling

GraphQL returns errors in a standard envelope. Always validate:

```json
{
  "errors": [
    {
      "message": "User not found",
      "locations": [{ "line": 2, "column": 3 }],
      "path": ["user"],
      "extensions": {
        "code": "NOT_FOUND"
      }
    }
  ],
  "data": { "user": null }
}
```

### Error Test Checklist

- **Validation errors** (missing required args) — `extensions.code` is `GRAPHQL_VALIDATION_FAILED`.
- **Authentication errors** — `extensions.code` is `UNAUTHENTICATED`.
- **Authorization errors** — `extensions.code` is `FORBIDDEN`.
- **Not found** — `data` contains `null` for the field; error in `errors` array.
- **Internal error** — `extensions.code` is `INTERNAL_SERVER_ERROR`; no stack trace or internals in `message`.
- **Partial errors** — When one field in a query fails, verify that other fields still resolve and partial data is returned.

---

## 6. Security-Specific Tests

| Concern | Test |
|---------|------|
| **Query depth** | Send deeply nested query; verify depth limit enforcement |
| **Query complexity** | Send query selecting many connections; verify complexity limit |
| **Batching abuse** | Send array of many queries in one request; verify batch limit |
| **Introspection** | In production, introspection may be disabled; verify `__schema` query is rejected |
| **Field-level auth** | Query a field the user doesn't have access to; verify null or error |
| **Injection in arguments** | Send SQL/NoSQL injection strings in argument values; verify safe handling |

---

## 7. Schema Stitching / Federation

If the API uses Apollo Federation or schema stitching:

- Test `@key` fields resolve correctly across services.
- Test `@external` fields are populated by the owning service.
- Test that a gateway timeout on one subgraph returns partial data, not a full failure.
- Verify `_entities` query resolves representations correctly.
