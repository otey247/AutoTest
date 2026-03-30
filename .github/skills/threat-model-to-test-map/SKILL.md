---
name: threat-model-to-test-map
description: 'Map identified threats from threat models to concrete test actions and verification steps. Use when translating STRIDE analysis into test cases, converting threat model findings to security test plans, prioritizing security tests by threat risk, or ensuring all identified threats have corresponding tests.'
---

# Threat Model to Test Map

Map identified threats from threat models to concrete, executable test actions.

## When to Use

- When translating STRIDE or other threat analysis into test cases
- When converting threat model findings to security test plans
- When ensuring all identified threats have test coverage
- When prioritizing security tests by threat risk level

## Procedure

1. Gather the threat model (STRIDE analysis, attack trees, or threat list)
2. For each identified threat:
   - Determine the test type needed (manual, automated, tool-based)
   - Define specific test scenarios that verify the mitigation works
   - Identify the test level (unit, integration, system, penetration)
   - Assign a testing priority based on threat risk
3. Map threats to test categories:
   - Spoofing → Authentication tests
   - Tampering → Integrity and input validation tests
   - Repudiation → Audit logging tests
   - Information disclosure → Data protection and access control tests
   - Denial of service → Availability and resilience tests
   - Elevation of privilege → Authorization tests
4. Identify gaps where threats have no corresponding tests

## Output Format

```markdown
# Threat-to-Test Map: [System]

## Mapping

| Threat ID | Threat | STRIDE | Risk | Test Type | Test Scenario | Test Level | Status |
|-----------|--------|--------|------|-----------|---------------|-----------|--------|
| T1 | [Threat] | [S/T/R/I/D/E] | [H/M/L] | [Manual/Auto/Tool] | [Scenario] | [Unit/Int/Sys/Pen] | [Covered/Gap] |

## Coverage Summary
- Threats identified: [N]
- Threats with tests: [N]
- Coverage gaps: [N]

## Priority Actions
1. [Highest risk uncovered threat]
```
