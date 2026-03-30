---
name: soak-test-checklist
description: 'Generate checklists for soak (endurance) testing that validates system stability under sustained load over extended periods. Use when checking for memory leaks, resource exhaustion, connection pool degradation, or other issues that only appear after hours of continuous operation.'
---

# Soak Test Checklist

Generate checklists for soak testing that validates system stability under sustained load over extended periods.

## When to Use

- When checking for memory leaks that appear over time
- When validating connection pool behavior under sustained use
- When looking for resource exhaustion issues
- When verifying system stability for 24/7 operations

## Procedure

1. Define soak test parameters:
   - Load level (typically normal production load)
   - Duration (hours — typically 4-24 hours)
   - Monitoring interval (how often to check metrics)
2. Define monitoring checklist:
   - Memory usage trend (should be stable, not growing)
   - CPU usage trend (should be stable)
   - Connection pool status (available vs in-use)
   - Thread count (should be stable)
   - Disk space (log growth, temp files)
   - Response time trend (should not degrade)
   - Error rate trend (should remain near zero)
3. Define pass/fail criteria

## Output Format

```markdown
# Soak Test Checklist: [System]

## Configuration
- Load level: [N concurrent users / X req/s]
- Duration: [X hours]
- Monitoring interval: [every X minutes]

## Pre-Test
- [ ] Baseline metrics captured
- [ ] Monitoring dashboards configured
- [ ] Alert thresholds set
- [ ] Log rotation confirmed

## During Test — Monitor Every [X] Minutes
- [ ] Memory usage: stable (not growing > [X%/hour])
- [ ] CPU usage: within [X%]
- [ ] Response time P95: within [Xms]
- [ ] Error rate: below [X%]
- [ ] Connection pool: [X] available
- [ ] Thread count: stable
- [ ] Disk space: adequate

## Post-Test
- [ ] Compare start vs end metrics
- [ ] Review logs for warnings or errors
- [ ] Check for resource leaks
- [ ] Document findings

## Pass Criteria
- No memory growth trend exceeding [X%]
- No response time degradation exceeding [X%]
- Error rate stays below [X%]
- All resources return to baseline after test
```
