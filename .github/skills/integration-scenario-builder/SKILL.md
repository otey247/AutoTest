---
name: integration-scenario-builder
description: 'Design integration test scenarios for service interactions, data flows, and system seams. Use when defining how services should interact, creating integration test plans, identifying integration failure modes, or testing data flow across service boundaries.'
---

# Integration Scenario Builder

Design integration test scenarios that validate interactions between services, modules, and system boundaries.

## When to Use

- When defining integration test scenarios for service interactions
- When testing data flow across service boundaries
- When identifying potential integration failure modes
- When creating integration test plans

## Procedure

1. Map the integration points:
   - Service-to-service API calls
   - Database read/write interactions
   - Message queue publish/subscribe flows
   - File-based integrations
   - External API dependencies
2. For each integration point, design scenarios covering:
   - **Happy path**: Normal successful interaction
   - **Timeout**: Service takes too long to respond
   - **Error response**: Service returns error status
   - **Malformed response**: Service returns unexpected data
   - **Unavailable**: Service is down or unreachable
   - **Rate limiting**: Service returns 429 or equivalent
   - **Data consistency**: Eventual consistency scenarios
   - **Ordering**: Message ordering and idempotency
3. Define setup requirements (test data, service stubs, environment config)
4. Prioritize scenarios by failure likelihood and business impact

## Output Format

```markdown
# Integration Scenarios: [Service A] ↔ [Service B]

## Integration Points
| Source | Target | Protocol | Data Flow |
|--------|--------|----------|-----------|

## Scenarios

### Scenario 1: [Name]
- **Integration point**: [Source → Target]
- **Category**: [Happy path / Error / Timeout / Unavailable]
- **Preconditions**: [Setup required]
- **Steps**: [Action sequence]
- **Expected result**: [What should happen]
- **Priority**: [High / Medium / Low]
```
