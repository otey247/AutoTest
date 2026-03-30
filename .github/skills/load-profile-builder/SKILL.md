---
name: load-profile-builder
description: 'Build realistic load profiles for performance testing based on production patterns or business projections. Use when defining user concurrency patterns, creating workload models, translating business scenarios to load patterns, or modeling traffic distribution across endpoints.'
---

# Load Profile Builder

Build realistic load profiles for performance testing based on production patterns or business projections.

## When to Use

- When defining user concurrency patterns for load tests
- When creating workload models from production data
- When translating business scenarios to technical load patterns
- When modeling traffic distribution across API endpoints

## Procedure

1. Identify data sources for load modeling:
   - Production traffic logs and analytics
   - Business projections for growth
   - Peak usage patterns (time of day, day of week, seasonal)
   - Marketing events or launches that drive traffic spikes
2. Define the workload model:
   - **User types**: Different user personas with different behaviors
   - **Transaction mix**: Ratio of different operations (browse: 60%, search: 25%, buy: 15%)
   - **Think time**: Pause between user actions
   - **Session duration**: Average time users stay active
   - **Concurrency curve**: How users ramp up and down over time
3. Create specific load profiles:
   - **Steady state**: Normal production load
   - **Peak hour**: Highest expected load
   - **Growth**: Projected load at 3x, 5x, 10x
   - **Spike**: Sudden burst pattern

## Output Format

```markdown
# Load Profile: [System/Scenario]

## User Mix
| User Type | % of Total | Key Actions | Think Time |
|-----------|-----------|-------------|-----------|

## Transaction Mix
| Transaction | % of Load | Avg Response Target |
|------------|----------|-------------------|

## Concurrency Profiles
### Steady State
- Concurrent users: [N]
- Duration: [X] hours

### Peak
- Concurrent users: [N]
- Duration: [X] minutes
- Ramp-up: [pattern]
```
