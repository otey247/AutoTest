---
name: sanity-pack-generator
description: 'Generate sanity test packs for narrow validation of a specific area after a change or fix. Use when verifying a bug fix is stable, checking a specific feature area after changes, creating focused test sets for hotfix validation, or confirming a change appears stable before broader regression.'
---

# Sanity Pack Generator

Generate focused sanity test packs that validate a specific area after a change or fix.

## When to Use

- When verifying a bug fix is stable before broader regression
- When a specific feature area changed and needs targeted validation
- When creating focused test sets for hotfix validation
- When confirming stability of a narrow area

## Procedure

1. Identify the change scope — what was modified?
2. Determine the directly affected functionality
3. Identify closely related functionality that could be impacted
4. Design narrow, targeted tests:
   - Test the changed behavior directly
   - Test immediately adjacent functionality
   - Test the most likely regression points
   - Keep the scope tight — this is not full regression
5. Define the boundary — what is explicitly out of scope

## Output Format

```markdown
# Sanity Test Pack: [Change/Fix Description]

## Change Scope
- [What was changed]

## Sanity Tests
| # | Test | Area | Verifies | Priority |
|---|------|------|----------|----------|
| 1 | [Test name] | [Changed area] | [Direct change validation] | Must-run |
| 2 | [Test name] | [Adjacent area] | [Regression check] | Must-run |
| 3 | [Test name] | [Related area] | [Impact check] | Should-run |

## Out of Scope
- [What is explicitly not covered and why]

## Graduation
- If sanity passes → proceed to [broader regression / release]
```
