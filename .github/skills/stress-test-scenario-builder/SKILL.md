---
name: stress-test-scenario-builder
description: 'Build stress test scenarios that push the system beyond expected limits to find breaking points. Use when identifying system capacity limits, testing graceful degradation behavior, finding memory leaks or resource exhaustion issues, or validating system behavior under extreme conditions.'
---

# Stress Test Scenario Builder

Build stress test scenarios that push the system beyond expected limits to find breaking points and degradation behavior.

## When to Use

- When identifying system capacity limits
- When testing graceful degradation under overload
- When finding memory leaks or resource exhaustion
- When validating system behavior under extreme conditions

## Procedure

1. Identify stress dimensions:
   - **Volume**: More concurrent users than expected capacity
   - **Data**: Larger datasets than normal
   - **Rate**: Higher request rate than expected
   - **Duration**: Sustained load over extended periods
   - **Resources**: Reduced CPU, memory, disk, or network
2. Design stress scenarios:
   - Gradually increase load until errors appear or performance degrades
   - Hold at peak stress to observe stability
   - Remove stress to verify recovery
3. Define observation points:
   - Response time degradation curve
   - Error rate increase pattern
   - Resource utilization (CPU, memory, connections, threads)
   - Recovery time after stress removal
4. Define acceptable degradation vs unacceptable failure

## Output Format

```markdown
# Stress Test Scenarios: [System]

## Scenarios

### Scenario 1: [Name]
- **Stress dimension**: [Volume / Data / Rate / Resources]
- **Starting load**: [baseline]
- **Step increase**: [increment]
- **Max load**: [target]
- **Hold duration**: [time at max]
- **Observe**: [what to measure]
- **Acceptable degradation**: [threshold]
- **Failure threshold**: [when to stop]

## Recovery Test
- Remove stress → system recovers within [X] minutes
```
