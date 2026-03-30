---
name: production-verification-checklist
description: 'Generate production verification checklists to validate system behavior in the production environment after deployment. Use when doing post-deployment production verification, validating production-specific configurations, checking production data flows, or verifying production monitoring and alerting.'
---

# Production Verification Checklist

Validate system behavior in the production environment after deployment.

## When to Use

- When verifying production-specific behavior after deployment
- When validating production configurations and integrations
- When checking production data flows work correctly
- When confirming production monitoring is operational

## Procedure

1. Define production-specific checks that differ from staging:
   - Production URLs and endpoints
   - Production integrations (payment, email, SMS)
   - Production data stores and caches
   - Production CDN and load balancers
2. Verify production configurations:
   - Feature flags are set correctly
   - Rate limits are configured
   - Caching is enabled and working
3. Validate production integrations:
   - Third-party services are connected
   - Webhooks are registered
   - Data sync is operational

## Output Format

```markdown
# Production Verification: [Release Version]

## Environment Validation
- [ ] Correct version deployed: [expected version]
- [ ] Feature flags configured correctly
- [ ] Environment variables set correctly
- [ ] SSL certificates valid

## Integration Verification
- [ ] Payment gateway connected
- [ ] Email service operational
- [ ] External API integrations responding
- [ ] Webhook endpoints registered

## Data Verification
- [ ] Database connections healthy
- [ ] Cache is populated and responsive
- [ ] Search indexes up to date
- [ ] Data migrations completed successfully

## Monitoring Verification
- [ ] Metrics flowing to monitoring system
- [ ] Dashboards showing real-time data
- [ ] Alerts are active and routing correctly
- [ ] Log aggregation is working
```
