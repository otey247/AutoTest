---
name: performance-test-plan-generator
description: 'Generate performance test plans that define objectives, load profiles, acceptance criteria, and tooling. Use when planning performance testing for a release, defining SLA validation tests, creating load test strategies, or setting up performance baselines.'
---

# Performance Test Plan Generator

Generate performance test plans with objectives, load profiles, acceptance criteria, and tooling recommendations.

## When to Use

- When planning performance testing for a release or new feature
- When defining SLA validation tests
- When creating a load testing strategy
- When setting up performance baselines

## Procedure

1. Gather performance requirements:
   - Response time targets (P50, P95, P99)
   - Throughput targets (requests/second, transactions/minute)
   - Concurrency requirements (simultaneous users)
   - Resource utilization limits (CPU, memory, network)
2. Define test types needed:
   - **Load test**: Expected production load
   - **Stress test**: Beyond expected limits to find breaking point
   - **Spike test**: Sudden burst of traffic
   - **Soak test**: Sustained load over hours to find leaks
   - **Scalability test**: Impact of adding/removing resources
3. Define load profiles for each test type
4. Identify critical transactions to measure
5. Define acceptance criteria with specific thresholds
6. Recommend tooling based on technology stack

## Output Format

```markdown
# Performance Test Plan: [System/Feature]

## Objectives
- [What we are measuring and why]

## Acceptance Criteria
| Metric | Target | Critical Threshold |
|--------|--------|-------------------|
| Response time (P95) | < [X]ms | < [Y]ms |
| Throughput | [N] req/s | [M] req/s |
| Error rate | < [X%] | < [Y%] |

## Test Types and Profiles
### Load Test
- Users: [N] concurrent
- Duration: [X] minutes
- Ramp-up: [X] minutes

## Critical Transactions
| Transaction | Weight | Target Response Time |
|------------|--------|---------------------|

## Tooling
- Recommended: [tool(s)]
```
