---
name: boundary-value-test-builder
description: 'Generate boundary value test cases for inputs with defined ranges or constraints. Use when testing numeric limits, string length boundaries, date ranges, collection size limits, or any input with minimum/maximum constraints.'
---

# Boundary Value Test Builder

Generate boundary value test cases for inputs with defined ranges, limits, or constraints.

## When to Use

- When inputs have defined minimum/maximum values
- When testing string length constraints
- When validating date ranges or numeric limits
- When testing collection size boundaries

## Procedure

1. Identify input parameters with defined boundaries
2. For each boundary, generate test values using the boundary value analysis technique:
   - **Minimum value**: The exact minimum allowed
   - **Just below minimum**: One step below the minimum (invalid)
   - **Just above minimum**: One step above the minimum (valid)
   - **Nominal value**: A typical mid-range value (valid)
   - **Just below maximum**: One step below the maximum (valid)
   - **Maximum value**: The exact maximum allowed
   - **Just above maximum**: One step above the maximum (invalid)
3. For multi-parameter functions, test boundaries for each parameter while keeping others at nominal values
4. Generate test cases with clear expected results (pass/fail)

## Output Format

```markdown
# Boundary Value Tests for [Function/Feature]

| Parameter | Boundary | Test Value | Expected Result | Category |
|-----------|----------|-----------|----------------|----------|
| [name] | Below min | [value] | Rejected/Error | Invalid |
| [name] | Min | [value] | Accepted | Valid |
| [name] | Above min | [value] | Accepted | Valid |
| [name] | Below max | [value] | Accepted | Valid |
| [name] | Max | [value] | Accepted | Valid |
| [name] | Above max | [value] | Rejected/Error | Invalid |
```
