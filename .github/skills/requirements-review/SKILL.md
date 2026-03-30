---
name: requirements-review
description: 'Review requirements, user stories, and acceptance criteria for ambiguity, contradiction, omission, and testability issues. Use when refining backlog items, reviewing stories before sprint planning, checking requirement quality, or validating that requirements are clear and verifiable enough to derive tests from.'
---

# Requirements Review

Systematically review requirements, user stories, and acceptance criteria to find quality issues before code is written.

## When to Use

- During backlog refinement or sprint planning
- When new requirements or user stories arrive
- When someone asks "is this requirement clear enough?"
- Before deriving test cases from requirements

## Procedure

1. Read each requirement, story, or acceptance criterion provided
2. Check for these common issues:
   - **Ambiguity**: Vague language like "fast," "user-friendly," "appropriate," "etc."
   - **Contradiction**: Conflicting statements across requirements
   - **Omission**: Missing error handling, edge cases, or boundary conditions
   - **Unverifiability**: Requirements that cannot be objectively tested
   - **Incompleteness**: Missing preconditions, postconditions, or data constraints
   - **Assumptions**: Unstated assumptions that could cause different interpretations
3. For each issue found, provide:
   - The specific text that is problematic
   - The type of issue (ambiguity, contradiction, omission, etc.)
   - Severity (high, medium, low)
   - A suggested improvement
4. Summarize overall quality and testability assessment

## Output Format

```markdown
# Requirements Review Report

## Summary
- Total items reviewed: [N]
- Issues found: [N] (High: [N], Medium: [N], Low: [N])
- Overall testability: [Good / Needs Improvement / Poor]

## Issues

### Issue 1: [Brief title]
- **Source**: "[exact text from requirement]"
- **Type**: [Ambiguity | Contradiction | Omission | Unverifiability | Incompleteness]
- **Severity**: [High | Medium | Low]
- **Problem**: [Why this is an issue]
- **Suggestion**: [How to fix it]

## Recommendations
- [List of recommended actions]
```
