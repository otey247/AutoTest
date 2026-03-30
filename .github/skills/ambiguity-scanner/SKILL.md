---
name: ambiguity-scanner
description: 'Scan requirements and documentation for ambiguous language that could cause different interpretations. Use when reviewing specs for vague terms, checking stories for unclear wording, finding requirements that use subjective or unmeasurable language, or preparing requirements for test derivation.'
---

# Ambiguity Scanner

Scan requirements, stories, and documentation for ambiguous language that could lead to different interpretations.

## When to Use

- When reviewing specs or stories for vague terms
- When checking requirements before test case derivation
- When preparing documentation for handoff to development

## Procedure

1. Scan the text for common ambiguity indicators:
   - **Vague adjectives/adverbs**: fast, slow, appropriate, reasonable, user-friendly, efficient, intuitive, quickly, properly, correctly, easily, etc.
   - **Incomplete lists**: "etc.", "and so on", "such as", "including but not limited to"
   - **Passive voice without actor**: "the data is processed" (by whom/what?)
   - **Undefined pronouns**: "it", "this", "they" without clear referent
   - **Missing quantities**: "multiple", "several", "many", "few", "some"
   - **Unbounded conditions**: "if needed", "as appropriate", "when possible"
   - **Implicit assumptions**: unstated preconditions or context
2. For each finding, explain why it is ambiguous
3. Suggest a specific, measurable replacement
4. Rate severity based on potential for misinterpretation

## Output Format

```markdown
# Ambiguity Scan Results

## Summary
- Items scanned: [N]
- Ambiguities found: [N]

## Findings

| # | Original Text | Ambiguity Type | Severity | Suggested Fix |
|---|--------------|---------------|----------|---------------|
| 1 | "System should respond quickly" | Vague adjective | High | "System responds within 200ms at P95" |
```
