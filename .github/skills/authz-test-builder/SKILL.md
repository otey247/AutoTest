---
name: authz-test-builder
description: 'Build test scenarios for authorization and access control including role-based access, privilege escalation, horizontal access, and resource-level permissions. Use when testing RBAC implementation, checking for privilege escalation vulnerabilities, testing multi-tenant isolation, or verifying API endpoint authorization.'
---

# Authorization Test Builder

Build test scenarios for authorization and access control to prevent privilege escalation and unauthorized access.

## When to Use

- When testing role-based access control (RBAC)
- When checking for horizontal and vertical privilege escalation
- When testing multi-tenant data isolation
- When verifying API endpoint authorization

## Procedure

1. Map the authorization model:
   - Roles and their permissions
   - Resource ownership and access rules
   - Tenant boundaries (if multi-tenant)
   - Special permissions (admin, superuser)
2. Generate test scenarios:
   - **Vertical escalation**: Lower-privilege user accesses higher-privilege functions
   - **Horizontal escalation**: User accesses another user's resources
   - **Missing authorization**: Endpoint accessible without any auth check
   - **IDOR**: Changing resource IDs to access others' data
   - **Role boundary**: Each role can only do what it's allowed
   - **Permission combinations**: Multiple permissions interact correctly
   - **Tenant isolation**: User from tenant A cannot see tenant B's data
   - **API authorization**: Direct API calls respect same rules as UI
3. Test with the principle of least privilege — verify denials, not just allows

## Output Format

```markdown
# Authorization Tests: [System]

## Role-Permission Matrix
| Action | Admin | Manager | User | Guest |
|--------|-------|---------|------|-------|
| [action] | ✅ | ✅ | ❌ | ❌ |

## Test Scenarios

| # | Category | As [Role] | Attempt | Expected | Priority |
|---|----------|-----------|---------|----------|----------|
| 1 | Vertical | User | Access admin panel | 403 Forbidden | Critical |
| 2 | Horizontal | User A | Access User B's order | 403 Forbidden | Critical |
| 3 | IDOR | User | Change order ID in URL | 403 Forbidden | Critical |
```
