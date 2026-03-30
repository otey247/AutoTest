---
name: acceptance-criteria-hardening
description: 'Strengthen acceptance criteria to make them specific, measurable, and testable. Use when stories have vague or weak acceptance criteria, when UAT scenarios need clear pass/fail conditions, or when preparing acceptance criteria for test derivation.'
---

# Acceptance Criteria Hardening

Transform vague or incomplete acceptance criteria into specific, measurable, testable conditions.

## When to Use

- When user stories have vague acceptance criteria like "should work correctly"
- When preparing stories for test case derivation
- When UAT scenarios need clear pass/fail conditions
- When acceptance criteria lack boundary conditions or error cases

## Procedure

1. Read the existing acceptance criteria
2. For each criterion, evaluate against the SMART-T checklist:
   - **Specific**: Does it describe a concrete behavior?
   - **Measurable**: Can you objectively determine pass/fail?
   - **Achievable**: Is it realistic within the scope?
   - **Relevant**: Does it trace to a business need?
   - **Testable**: Can an automated or manual test verify it?
3. Rewrite weak criteria with:
   - Specific inputs and expected outputs
   - Concrete numeric thresholds where applicable
   - Explicit error and boundary conditions
   - Clear preconditions and postconditions
4. Add missing criteria for:
   - Error handling and failure modes
   - Boundary and edge cases
   - Performance or response time expectations (if applicable)
   - Security considerations (if applicable)

## Output Format

```markdown
# Hardened Acceptance Criteria

## Original: "[original text]"
### Issues
- [What's wrong with the original]

### Hardened Version
- GIVEN [precondition] WHEN [action] THEN [expected result]
- GIVEN [precondition] WHEN [error condition] THEN [error handling]

### Added Criteria
- [New boundary/edge case criteria]
- [New error handling criteria]
```
