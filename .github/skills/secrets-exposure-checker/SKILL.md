---
name: secrets-exposure-checker
description: 'Check for exposed secrets, credentials, and sensitive data in code, configuration, and logs. Use when scanning repositories for hardcoded secrets, checking for credential leaks, verifying that sensitive data is not logged, or auditing secret management practices.'
---

# Secrets Exposure Checker

Check for exposed secrets, credentials, and sensitive data in code, configuration, and logs.

## When to Use

- When scanning repositories for hardcoded secrets
- When checking for credential leaks in code or config
- When verifying that sensitive data is not logged
- When auditing secret management practices

## Procedure

1. Scan for common secret patterns:
   - **API keys**: Long alphanumeric strings in code or config
   - **Passwords**: Password variables with literal values
   - **Connection strings**: Database URLs with embedded credentials
   - **Tokens**: JWT tokens, OAuth tokens, bearer tokens in code
   - **Private keys**: PEM-encoded private keys
   - **Cloud credentials**: AWS access keys, GCP service account keys
   - **Webhook URLs**: URLs containing secrets
2. Check common exposure locations:
   - Source code files
   - Configuration files (not using environment variables)
   - Docker files and compose files
   - CI/CD pipeline definitions
   - Log output and error messages
   - Git history (previously committed then removed)
   - Documentation and README files
3. Verify secret management practices:
   - Secrets stored in vault or environment variables
   - .gitignore covers sensitive files
   - CI/CD uses secret management, not inline values
4. Recommend remediation for any findings

## Output Format

```markdown
# Secrets Exposure Check: [Repository/System]

## Findings

| # | Location | Secret Type | Severity | Recommendation |
|---|----------|-----------|----------|---------------|
| 1 | [file:line] | [API key / password / token] | [Critical] | [Rotate and move to vault] |

## Secret Management Assessment
- [ ] No hardcoded secrets in source code
- [ ] Secrets stored in vault/secret manager
- [ ] .gitignore covers sensitive files
- [ ] CI/CD uses secret variables
- [ ] Logs do not contain sensitive data
- [ ] Git history is clean of secrets

## Remediation Steps
1. [Most urgent action]
```
