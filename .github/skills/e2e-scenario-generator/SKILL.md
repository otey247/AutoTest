---
name: e2e-scenario-generator
description: 'Generate end-to-end test scenarios that cover complete business workflows across multiple systems. Use when designing e2e test suites, creating user journey tests, validating cross-system business flows, or building comprehensive acceptance test scenarios.'
---

# E2E Scenario Generator

Generate end-to-end test scenarios that validate complete business workflows from start to finish.

## When to Use

- When designing end-to-end test suites for releases
- When creating user journey tests
- When validating cross-system business flows
- When building comprehensive acceptance test scenarios

## Procedure

1. Identify the key business workflows to cover:
   - Core user journeys (signup, purchase, account management)
   - Critical business processes (order fulfillment, payment processing)
   - Cross-system flows (front-end → API → database → external service)
2. For each workflow, design scenarios:
   - **Happy path**: Complete successful flow from start to finish
   - **Alternative paths**: Valid variations of the workflow
   - **Error recovery**: How the system handles mid-flow failures
   - **Edge cases**: Unusual but valid combinations
3. Define for each scenario:
   - Preconditions and test data requirements
   - Step-by-step actions from the user's perspective
   - Verification points at each critical step
   - Expected final state
4. Prioritize by business criticality and usage frequency

## Output Format

```markdown
# E2E Scenarios: [Workflow Name]

## Scenario 1: [Descriptive Name]
- **Priority**: [Critical / High / Medium / Low]
- **Preconditions**: [Setup required]
- **Steps**:
  1. [User action] → Verify: [expected result]
  2. [User action] → Verify: [expected result]
  3. [User action] → Verify: [expected result]
- **Final state**: [Expected end state]
- **Systems involved**: [List of systems]
- **Estimated duration**: [Time]
```
