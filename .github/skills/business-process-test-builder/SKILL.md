---
name: business-process-test-builder
description: 'Build test scenarios for complex business processes and multi-step workflows. Use when testing business rules, validating approval workflows, testing multi-step transactions, or verifying process flows with branching logic and exception handling.'
---

# Business Process Test Builder

Build test scenarios for complex business processes that involve multiple steps, roles, and decision points.

## When to Use

- When testing business processes with multiple steps and decision points
- When validating approval workflows and role-based actions
- When testing multi-step transactions with rollback requirements
- When verifying process flows with branching logic

## Procedure

1. Map the business process:
   - Identify all steps in the process
   - Identify decision points and branching logic
   - Identify roles and actors involved
   - Identify state transitions
2. Generate test scenarios for:
   - **Straight-through processing**: All steps complete successfully
   - **Each decision branch**: Every branch path is exercised
   - **Role variations**: Different actors performing allowed/disallowed actions
   - **Exception handling**: What happens when a step fails mid-process
   - **Cancellation**: Process cancelled at each stage
   - **Timeout**: Process abandoned or expired
   - **Parallel paths**: Concurrent process instances
3. Define verification points at each state transition
4. Include test data requirements for each scenario

## Output Format

```markdown
# Business Process Tests: [Process Name]

## Process Map
[Step 1] → [Decision] → [Step 2a] or [Step 2b] → [Step 3] → [End]

## Scenarios

### Scenario 1: [Name]
- **Path**: [Which branches]
- **Actors**: [Roles involved]
- **Steps**:
  1. [Action] → State: [expected state]
  2. [Decision] → Branch: [which path]
  3. [Action] → State: [expected state]
- **Final state**: [Expected outcome]
- **Test data**: [Requirements]
```
