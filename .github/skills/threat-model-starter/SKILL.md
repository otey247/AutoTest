---
name: threat-model-starter
description: 'Create initial threat models by examining design and data flows to identify security risks. Use when starting threat modeling for a new feature, identifying trust boundaries and attack surfaces, generating STRIDE-based threat analysis, or preparing security requirements before implementation.'
---

# Threat Model Starter

Create initial threat models by examining design, data flows, and trust boundaries to identify security risks early.

## When to Use

- When a new feature or system needs threat analysis
- When identifying trust boundaries and attack surfaces
- When preparing security requirements before implementation
- When updating threat models after architecture changes

## Procedure

1. Identify the system components and their interactions
2. Map data flows between components, identifying:
   - Entry points (user inputs, API endpoints, file uploads)
   - Trust boundaries (internal vs external, auth boundaries)
   - Data stores (databases, caches, file systems)
   - External dependencies (third-party APIs, libraries)
3. Apply STRIDE analysis to each component and flow:
   - **Spoofing**: Can an attacker impersonate a user or system?
   - **Tampering**: Can data be modified in transit or at rest?
   - **Repudiation**: Can actions be denied without evidence?
   - **Information disclosure**: Can sensitive data leak?
   - **Denial of service**: Can the system be made unavailable?
   - **Elevation of privilege**: Can an attacker gain higher access?
4. Rate each threat by likelihood and impact
5. Recommend mitigations and test actions

## Output Format

```markdown
# Threat Model: [System/Feature Name]

## System Overview
[Brief description of components and data flows]

## Trust Boundaries
- [Boundary 1: description]
- [Boundary 2: description]

## Threats

| ID | Component | STRIDE | Threat | Likelihood | Impact | Mitigation | Test Action |
|----|-----------|--------|--------|-----------|--------|-----------|-------------|
| T1 | [Component] | [S/T/R/I/D/E] | [Description] | [H/M/L] | [H/M/L] | [Mitigation] | [Test to verify] |

## Priority Recommendations
1. [Highest risk threat and recommended action]
```
