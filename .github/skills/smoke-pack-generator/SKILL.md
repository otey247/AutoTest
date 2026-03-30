---
name: smoke-pack-generator
description: 'Generate smoke test packs for quick validation that critical system functions work after a build or deployment. Use when creating deployment verification tests, building quick sanity checks for CI/CD pipelines, or defining the minimal test set that must pass before deeper testing begins.'
---

# Smoke Pack Generator

Generate smoke test packs that quickly verify critical system functions after a build or deployment.

## When to Use

- When defining post-deployment verification tests
- When building quick validation suites for CI/CD pipelines
- When determining the minimum test set that must pass before deeper testing

## Procedure

1. Identify the critical system functions:
   - Application starts and is accessible
   - Authentication works (login/logout)
   - Core navigation is functional
   - Primary API endpoints respond
   - Database connectivity is healthy
   - Key business operations complete (e.g., create, read)
2. Design shallow, broad tests — breadth over depth:
   - Each test should be fast (seconds, not minutes)
   - Each test should verify one critical capability
   - Tests should be independent and order-insensitive
3. Define pass/fail criteria:
   - All smoke tests must pass for deeper testing to proceed
   - Any smoke failure blocks the pipeline

## Output Format

```markdown
# Smoke Test Pack: [Environment/Release]

## Critical Path Tests
| # | Test | What It Verifies | Expected Result | Max Duration |
|---|------|-----------------|----------------|-------------|
| 1 | App health check | Application is running | HTTP 200 on /health | 5s |
| 2 | Login flow | Authentication works | Successful login | 10s |
| 3 | Main page loads | UI is functional | Page renders correctly | 10s |

## Execution
- **When**: After every deployment
- **Blocking**: Yes — failures block further testing
- **Estimated total time**: [N minutes]
```
