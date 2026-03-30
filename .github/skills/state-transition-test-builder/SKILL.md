---
name: state-transition-test-builder
description: 'Build test scenarios from state transition models to ensure all valid and invalid state changes are covered. Use when testing stateful systems, validating workflow state machines, testing order lifecycles, or verifying that invalid state transitions are properly rejected.'
---

# State Transition Test Builder

Build test scenarios from state transition models to verify all valid and invalid state changes.

## When to Use

- When testing stateful systems (orders, workflows, tickets)
- When validating state machine behavior
- When testing lifecycle management (draft → active → archived)
- When verifying invalid state transitions are rejected

## Procedure

1. Define the state model:
   - List all possible states
   - List all events/triggers that cause transitions
   - Map valid transitions (state + event → new state)
   - Identify guards/conditions on transitions
2. Generate a state transition table
3. Create test scenarios for:
   - **All valid transitions**: Each allowed state change
   - **All invalid transitions**: Each state + event combination that should be rejected
   - **Guard conditions**: Transitions that depend on conditions being true/false
   - **Boundary states**: Initial state, terminal states
   - **Round trips**: Sequences that return to the starting state
4. Prioritize by business importance and defect likelihood

## Output Format

```markdown
# State Transition Tests: [Entity]

## State Model
| Current State | Event | Guard | Next State |
|--------------|-------|-------|-----------|
| [state] | [event] | [condition] | [new state] |

## Valid Transition Tests
| # | From | Event | To | Expected Result |
|---|------|-------|----|----------------|

## Invalid Transition Tests
| # | From | Event | Expected Result |
|---|------|-------|----------------|
| 1 | Completed | Submit | Rejected with error |

## Sequence Tests
| # | Sequence | Purpose |
|---|----------|---------|
| 1 | Draft → Active → Completed | Happy path lifecycle |
```
