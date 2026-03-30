---
name: failover-test-planner
description: 'Plan failover and resilience tests that validate system behavior during component failures. Use when testing disaster recovery, validating high availability configurations, testing circuit breaker behavior, or verifying that the system handles infrastructure failures gracefully.'
---

# Failover Test Planner

Plan failover and resilience tests that validate system behavior during component failures.

## When to Use

- When testing disaster recovery procedures
- When validating high availability configurations
- When testing circuit breaker and retry behavior
- When verifying graceful degradation during outages

## Procedure

1. Identify critical system components and dependencies
2. For each component, design failure scenarios:
   - **Complete outage**: Component is down
   - **Partial failure**: Component responds slowly or intermittently
   - **Network partition**: Component is unreachable
   - **Data corruption**: Component returns invalid data
   - **Resource exhaustion**: Component runs out of connections/memory
3. Define expected behavior for each failure:
   - Automatic failover to backup
   - Graceful degradation (reduced functionality)
   - Circuit breaker activation
   - Retry with backoff
   - User-facing error handling
4. Define recovery validation:
   - System detects component recovery
   - Traffic shifts back to primary
   - No data loss or corruption

## Output Format

```markdown
# Failover Test Plan: [System]

## Component Failure Scenarios

| Component | Failure Type | Expected Behavior | Recovery Behavior | Priority |
|-----------|-------------|------------------|------------------|----------|
| [DB] | Complete outage | Failover to replica | Auto-reconnect | Critical |

## Test Procedures

### Test 1: [Component] — [Failure Type]
1. **Precondition**: [System state and monitoring setup]
2. **Inject failure**: [How to simulate the failure]
3. **Observe**: [What to measure during failure]
4. **Validate**: [Expected degradation behavior]
5. **Recover**: [How to restore the component]
6. **Verify recovery**: [System returns to normal]

## Success Criteria
- [ ] Failover completes within [X] seconds
- [ ] No data loss during failover
- [ ] Recovery is automatic and complete
```
