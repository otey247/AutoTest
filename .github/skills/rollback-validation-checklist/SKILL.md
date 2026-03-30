---
name: rollback-validation-checklist
description: 'Generate rollback validation checklists to verify that a rollback was executed successfully and the system is restored. Use when testing rollback procedures, verifying rollback completeness after a failed deployment, or validating that data integrity is maintained during rollback.'
---

# Rollback Validation Checklist

Generate rollback validation checklists to verify successful system restoration after a failed deployment.

## When to Use

- When testing rollback procedures before go-live
- When verifying rollback completeness after a failed deployment
- When validating data integrity during rollback
- When creating rollback test plans

## Procedure

1. Define the rollback scope:
   - Application version rollback
   - Database schema rollback (if applicable)
   - Configuration rollback
   - Infrastructure changes rollback
2. Generate validation checks for:
   - Application is running the previous version
   - All features of the previous version work
   - Database schema is compatible
   - Data integrity is maintained (no data loss)
   - Integrations are functioning
   - Background jobs are processing correctly
3. Define rollback-specific risks to check

## Output Format

```markdown
# Rollback Validation Checklist: [From Version] → [To Version]

## Pre-Rollback
- [ ] Rollback procedure documented
- [ ] Database rollback scripts tested
- [ ] Team notified of rollback
- [ ] Monitoring prepared for rollback

## Rollback Execution
- [ ] Application rolled back to [version]
- [ ] Database migration rolled back (if applicable)
- [ ] Configuration reverted
- [ ] CDN/cache purged (if applicable)

## Post-Rollback Validation
- [ ] Application version confirmed as [previous version]
- [ ] Health endpoints return 200
- [ ] Authentication works
- [ ] Core features functional
- [ ] Data integrity verified (no data loss)
- [ ] Integrations connected
- [ ] Background jobs processing
- [ ] Error rate returned to baseline

## Data Integrity
- [ ] Records created during failed deploy are handled
- [ ] No orphaned data from incomplete migrations
- [ ] Audit trail is consistent
```
