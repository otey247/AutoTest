---
name: observability-validation-checklist
description: 'Generate checklists to validate that monitoring, logging, alerting, and tracing are working correctly. Use when verifying production readiness of observability stack, testing that alerts fire correctly, checking log completeness, or validating distributed tracing across services.'
---

# Observability Validation Checklist

Validate that monitoring, logging, alerting, and tracing are working correctly before and after deployment.

## When to Use

- When verifying observability readiness before release
- When testing that alerts fire correctly
- When checking log completeness and format
- When validating distributed tracing across services

## Procedure

1. Define observability requirements:
   - What metrics must be collected?
   - What alerts must be configured?
   - What log data must be available?
   - What traces must be captured?
2. Generate checklist for each pillar:
   - **Metrics**: Key business and technical metrics are collected and visualized
   - **Logging**: Structured logs with appropriate detail levels
   - **Alerting**: Alerts configured for critical conditions with appropriate thresholds
   - **Tracing**: Distributed traces connect requests across services
3. Define validation tests for each item

## Output Format

```markdown
# Observability Validation Checklist: [System]

## Metrics
- [ ] Application health metrics are collected (uptime, error rate)
- [ ] Business metrics are tracked (transactions, revenue)
- [ ] Infrastructure metrics are visible (CPU, memory, disk)
- [ ] Dashboard shows real-time data
- [ ] Historical data is retained for [X] days

## Logging
- [ ] Logs are structured (JSON format)
- [ ] Log levels are appropriate (no excessive DEBUG in production)
- [ ] Sensitive data is not logged (passwords, tokens, PII)
- [ ] Request correlation IDs are present
- [ ] Error logs include stack traces and context

## Alerting
- [ ] Critical alerts configured: [list]
- [ ] Alert routing is correct (right team, right channel)
- [ ] Alert thresholds are validated (not too noisy, not too quiet)
- [ ] Runbooks are linked to alerts
- [ ] Escalation paths are defined

## Tracing
- [ ] Traces span across service boundaries
- [ ] Trace context is propagated correctly
- [ ] Slow traces are identifiable
- [ ] Error traces are flagged
```
