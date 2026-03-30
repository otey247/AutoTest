---
name: operational-acceptance-checklist
description: 'Generate operational acceptance testing (OAT) checklists that verify the system can be run and supported in production. Use when validating backup and restore, monitoring and alerting, job scheduling, access administration, support processes, or other operational concerns before go-live.'
---

# Operational Acceptance Checklist

Generate operational acceptance testing checklists for production readiness validation.

## When to Use

- When validating that a system can be operated and supported in production
- When checking backup, restore, monitoring, and alerting capabilities
- When verifying operational procedures before go-live
- When assessing operations team readiness

## Procedure

1. Assess operational requirements for the system
2. Generate checklist items for:
   - **Backup and recovery**: Procedures exist and have been tested
   - **Monitoring**: Health checks, metrics, dashboards are configured
   - **Alerting**: Critical alerts route to the right team
   - **Logging**: Structured logs with appropriate retention
   - **Scaling**: Auto-scaling or manual scaling procedures documented
   - **Incident response**: Runbooks and escalation paths defined
   - **Access control**: Admin access is role-based and audited
   - **Job scheduling**: Batch jobs, cron jobs are configured and monitored
   - **Data management**: Retention, archival, and purge procedures
   - **Documentation**: Runbooks, architecture diagrams, dependency maps

## Output Format

```markdown
# Operational Acceptance Checklist: [System]

## Backup and Recovery
- [ ] Backup procedures documented and automated
- [ ] Restore tested successfully within RTO target ([X] hours)
- [ ] RPO validated (max [X] hours of data loss)
- [ ] Backup monitoring and alerting configured

## Monitoring and Alerting
- [ ] Health check endpoints configured
- [ ] Key metrics dashboards created
- [ ] Critical alerts configured and routed
- [ ] Alert thresholds validated

## Incident Response
- [ ] Runbooks created for common issues
- [ ] Escalation paths defined
- [ ] On-call rotation established
- [ ] Post-incident review process defined

## Access Control
- [ ] Admin access is role-based
- [ ] Access audit logging enabled
- [ ] Service accounts documented

## Documentation
- [ ] Architecture diagram current
- [ ] Dependency map maintained
- [ ] Operational runbooks complete
```
