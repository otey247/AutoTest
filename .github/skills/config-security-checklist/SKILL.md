---
name: config-security-checklist
description: 'Generate security configuration checklists for infrastructure and runtime environments. Use when auditing deployment configurations, checking for insecure defaults, verifying production security hardening, or reviewing cloud infrastructure security settings.'
---

# Configuration Security Checklist

Generate security configuration checklists for infrastructure, runtime, and deployment environments.

## When to Use

- When auditing deployment configurations for security
- When checking for insecure defaults in production
- When verifying security hardening measures
- When reviewing cloud infrastructure settings

## Procedure

1. Identify the technology stack and deployment environment
2. Generate checklist items for:
   - **Application configuration**:
     - Debug mode disabled in production
     - Verbose error messages disabled
     - Default credentials changed
     - Unnecessary features/endpoints disabled
   - **Transport security**:
     - HTTPS enforced
     - TLS version and cipher suites appropriate
     - HSTS header present
     - Certificate validity
   - **Headers and cookies**:
     - Security headers present (CSP, X-Frame-Options, X-Content-Type-Options)
     - Cookies have Secure, HttpOnly, SameSite flags
   - **Infrastructure**:
     - Unnecessary ports closed
     - Admin interfaces not publicly accessible
     - Storage buckets not publicly readable
     - Network segmentation appropriate
   - **Secrets management**:
     - No hardcoded secrets in code or config
     - Secrets stored in vault or secret manager
     - Secrets rotated regularly

## Output Format

```markdown
# Configuration Security Checklist: [Environment]

## Application
- [ ] Debug mode disabled
- [ ] Verbose errors disabled
- [ ] Default credentials changed
- [ ] Unnecessary endpoints removed

## Transport Security
- [ ] HTTPS enforced everywhere
- [ ] TLS 1.2+ only
- [ ] HSTS header present

## Security Headers
- [ ] Content-Security-Policy configured
- [ ] X-Frame-Options set
- [ ] X-Content-Type-Options: nosniff

## Infrastructure
- [ ] Unnecessary ports closed
- [ ] Admin panel restricted
- [ ] Storage permissions correct

## Secrets
- [ ] No hardcoded secrets
- [ ] Secrets in vault/manager
- [ ] Rotation policy in place
```
