---
name: release-smoke-checklist
description: 'Generate post-deployment smoke checklists to verify a release was deployed successfully. Use when verifying deployments to production or staging, creating post-release verification procedures, checking that critical functions work after deployment, or validating environment health after changes.'
---

# Release Smoke Checklist

Generate post-deployment smoke checklists to verify a release was deployed successfully.

## When to Use

- When verifying deployments to production or staging
- When creating post-release verification procedures
- When checking critical functions work after deployment
- When validating environment health after changes

## Procedure

1. Define the critical checks for post-deployment:
   - Application is accessible and responding
   - Authentication works
   - Core business functions are operational
   - Key integrations are connected
   - Data is intact (no migration issues)
   - Background jobs are running
   - Monitoring is receiving data
2. Order checks from most critical to least critical
3. Define rollback trigger conditions

## Output Format

```markdown
# Release Smoke Checklist: [Version/Release]

## Deployment Info
- Version: [version]
- Environment: [production/staging]
- Deploy time: [timestamp]
- Deployed by: [person/pipeline]

## Critical Checks (Block rollback window)
- [ ] Application health endpoint returns 200
- [ ] Homepage/login page loads
- [ ] Authentication flow works
- [ ] Primary API endpoints respond
- [ ] Database connectivity confirmed

## Functional Checks
- [ ] [Core function 1] works
- [ ] [Core function 2] works
- [ ] [Critical integration] is connected

## Infrastructure Checks
- [ ] Monitoring receiving metrics
- [ ] Logs are flowing
- [ ] Background workers are processing
- [ ] Queue consumers are active

## Rollback Triggers
- Any critical check fails → Immediate rollback
- More than [N] functional checks fail → Rollback
- Error rate exceeds [X%] → Rollback
```
