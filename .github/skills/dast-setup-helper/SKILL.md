---
name: dast-setup-helper
description: 'Help set up Dynamic Application Security Testing (DAST) scanning for web applications and APIs. Use when configuring DAST tools like OWASP ZAP or Burp Suite, defining scan scope and policies, integrating DAST into CI/CD pipelines, or interpreting DAST scan results.'
---

# DAST Setup Helper

Help set up and configure Dynamic Application Security Testing for web applications and APIs.

## When to Use

- When configuring DAST tools (OWASP ZAP, Burp Suite, Nuclei)
- When defining DAST scan scope and policies
- When integrating DAST into CI/CD pipelines
- When interpreting and triaging DAST results

## Procedure

1. Define scan scope:
   - Target URLs and applications
   - In-scope and out-of-scope paths
   - Authentication requirements
   - API specifications (if scanning APIs)
2. Choose scanning approach:
   - **Passive scan**: Observe traffic without active attacks
   - **Active scan**: Send attack payloads to find vulnerabilities
   - **API scan**: Scan based on OpenAPI/Swagger spec
   - **Authenticated scan**: Scan as authenticated user
3. Configure scan policies:
   - Which vulnerability classes to test
   - Scan strength and thoroughness
   - Rate limiting to avoid impact
   - Excluded parameters or endpoints
4. Plan result handling:
   - Severity thresholds for blocking releases
   - False positive management
   - Integration with issue tracking

## Output Format

```markdown
# DAST Configuration: [Application]

## Scan Scope
- Target: [URL(s)]
- Authentication: [Method]
- Excluded: [Paths to skip]

## Scan Configuration
| Scan Type | Tool | Policy | Schedule |
|----------|------|--------|---------|
| Passive | [tool] | [policy] | [per PR / nightly] |
| Active | [tool] | [policy] | [weekly / pre-release] |

## CI/CD Integration
- Pipeline stage: [where in the pipeline]
- Blocking threshold: [severity level]
- Results destination: [where findings are reported]

## Triage Process
- False positive handling: [process]
- Severity override criteria: [when to override]
```
