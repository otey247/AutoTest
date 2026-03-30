---
name: security-misuse-case-generator
description: 'Generate misuse and abuse case scenarios that describe how legitimate features could be exploited. Use when identifying abuse of business logic, creating security test scenarios from an attacker perspective, testing for workflow manipulation, or identifying ways users could game the system.'
---

# Security Misuse Case Generator

Generate misuse and abuse case scenarios that describe how legitimate features could be exploited by malicious actors.

## When to Use

- When identifying potential abuse of business logic
- When creating security test scenarios from an attacker's perspective
- When testing for workflow manipulation
- When preparing for security reviews or penetration tests

## Procedure

1. Identify the feature or workflow to analyze
2. For each feature, ask "how could this be abused?":
   - **Sequence abuse**: Skipping steps, reordering operations
   - **Replay abuse**: Repeating actions (apply discount twice, vote twice)
   - **Rate abuse**: Making many requests quickly (scraping, enumeration)
   - **Privilege abuse**: Using legitimate access for unintended purposes
   - **Data abuse**: Manipulating inputs to achieve unintended outcomes
   - **Logic bypass**: Finding alternative paths around controls
   - **Resource abuse**: Consuming excessive resources (storage, compute)
3. Write each misuse case with:
   - Actor (who would do this)
   - Motivation (why)
   - Attack steps (how)
   - Impact (what happens if successful)
   - Detection (how to notice it)
   - Prevention (how to stop it)

## Output Format

```markdown
# Misuse Cases: [Feature/System]

## Misuse Case 1: [Name]
- **Actor**: [Who — e.g., authenticated user, anonymous visitor]
- **Motivation**: [Why — e.g., financial gain, data theft]
- **Attack steps**:
  1. [Step]
  2. [Step]
- **Impact**: [Consequence if successful]
- **Detection**: [How to detect this abuse]
- **Prevention**: [Controls to prevent it]
- **Test scenario**: [How to test for this]
```
