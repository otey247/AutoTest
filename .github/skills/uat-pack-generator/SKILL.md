---
name: uat-pack-generator
description: 'Generate User Acceptance Testing (UAT) test packs with business scenarios derived from requirements. Use when preparing UAT for business stakeholders, translating requirements into user-facing test scenarios, creating UAT sign-off documentation, or building acceptance test suites for releases.'
---

# UAT Pack Generator

Generate User Acceptance Testing packs with business scenarios derived from requirements.

## When to Use

- When preparing UAT test packs for business stakeholders
- When translating requirements into user-facing test scenarios
- When creating UAT sign-off documentation
- When building acceptance test suites for releases

## Procedure

1. Gather requirements, user stories, and acceptance criteria
2. Translate each into user-facing test scenarios:
   - Use business language, not technical jargon
   - Include realistic test data examples
   - Define clear preconditions and expected outcomes
   - Cover both standard and exception flows
3. Organize scenarios by business process or feature area
4. Include pass/fail recording fields for each scenario
5. Add sign-off section for stakeholder approval

## Output Format

```markdown
# UAT Test Pack: [Feature/Release]

## Overview
- Release: [version/name]
- Test period: [dates]
- Tester(s): [business stakeholder names]

## Test Scenarios

### [Business Area 1]

#### Scenario 1: [Business-friendly name]
- **Preconditions**: [What must be true before testing]
- **Test data**: [Specific data to use]
- **Steps**:
  1. [User action in business language]
  2. [User action]
  3. [User action]
- **Expected result**: [What the user should see/experience]
- **Result**: ☐ Pass ☐ Fail
- **Notes**: [Space for comments]

## Sign-off
| Stakeholder | Role | Approved | Date | Signature |
|------------|------|----------|------|-----------|
```
