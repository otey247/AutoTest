---
name: risk-waiver-template
description: 'Generate risk waiver templates for documenting accepted risks when releasing with known issues. Use when deferring known defects to a later release, accepting residual security risks with mitigations, documenting risk acceptance decisions for compliance, or getting stakeholder sign-off on known issues.'
---

# Risk Waiver Template

Generate risk waiver templates for documenting accepted risks when releasing with known issues.

## When to Use

- When deferring known defects to a later release
- When accepting residual security risks with mitigations
- When documenting risk acceptance for compliance or audit
- When getting stakeholder sign-off on known issues

## Procedure

1. Document the known risk or issue
2. Assess the risk:
   - Severity and impact
   - Likelihood of occurrence
   - Affected users or functions
   - Workarounds available
3. Define mitigations in place
4. Set review timeline
5. Get stakeholder approval

## Output Format

```markdown
# Risk Waiver: [Issue/Risk Title]

## Risk Details
- **Issue ID**: [ID or reference]
- **Description**: [What the risk is]
- **Severity**: [Critical / High / Medium / Low]
- **Likelihood**: [High / Medium / Low]
- **Affected area**: [Features or users impacted]

## Impact Assessment
- **Business impact**: [Revenue, user experience, compliance]
- **Technical impact**: [System behavior, data integrity]
- **Scope**: [How many users/transactions affected]

## Mitigations
- [Mitigation 1: e.g., monitoring alert configured]
- [Mitigation 2: e.g., workaround documented for support team]
- [Mitigation 3: e.g., feature flag to disable if needed]

## Timeline
- **Deferred until**: [target date or release]
- **Review date**: [when to reassess]
- **Owner**: [who is responsible for resolution]

## Approval
| Stakeholder | Role | Decision | Date |
|------------|------|----------|------|
| [Name] | [Role] | Accepted / Rejected | [Date] |

## Conditions
- This waiver is valid until [date/release]
- Must be reviewed if [condition changes]
```
