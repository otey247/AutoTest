---
name: data-rule-extractor
description: 'Extract data validation rules and constraints from requirements, schemas, or code. Use when identifying field constraints, finding data validation gaps, deriving data-driven test cases, or documenting expected data formats and business rules for testing.'
---

# Data Rule Extractor

Extract data validation rules, constraints, and business rules from requirements, schemas, or code.

## When to Use

- When identifying field constraints for test data generation
- When finding gaps in data validation logic
- When deriving data-driven test cases
- When documenting expected data formats and business rules

## Procedure

1. Analyze the source material (requirements, schema definitions, code)
2. Extract rules for each data field:
   - **Type**: String, integer, decimal, date, boolean, enum, etc.
   - **Format**: Email, phone, postal code, date format, etc.
   - **Length**: Minimum, maximum, exact
   - **Range**: Minimum value, maximum value
   - **Required/Optional**: Whether the field must be present
   - **Default**: Default value if not provided
   - **Uniqueness**: Must be unique across records
   - **Relationships**: Foreign keys, dependent fields, conditional rules
   - **Business rules**: Calculated fields, validation logic, cross-field constraints
3. Identify rules that are implicit or missing
4. Map rules to test scenarios

## Output Format

```markdown
# Data Rules: [Entity/Feature]

## Field Rules

| Field | Type | Required | Format | Min | Max | Rules | Test Cases Needed |
|-------|------|----------|--------|-----|-----|-------|-------------------|
| [name] | [type] | [Y/N] | [format] | [min] | [max] | [rules] | [N] |

## Business Rules
| Rule ID | Description | Fields Involved | Test Scenarios |
|---------|-----------|----------------|----------------|

## Missing/Implicit Rules
- [Rule that should exist but is not documented]
```
