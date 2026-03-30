---
name: quality-gate-generator
description: 'Generate entry and exit criteria for test phases and quality gates. Use when defining what must be true before testing starts or before a phase can be considered complete, when setting up CI/CD quality gates, or when establishing release criteria.'
---

# Quality Gate Generator

Generate entry and exit criteria for test phases and quality gates across the SDLC.

## When to Use

- When defining phase entry/exit criteria
- When setting up CI/CD quality gates
- When establishing release criteria
- When teams need clear definitions of "done" for test phases

## Procedure

1. Identify the SDLC phase or quality gate being defined
2. Define **entry criteria** — what must be true before the phase begins:
   - Prerequisite artifacts available
   - Prior phase exit criteria met
   - Environment and tools ready
   - Test data prepared
3. Define **exit criteria** — what must be true to consider the phase complete:
   - Coverage targets met
   - No critical or high-severity open defects
   - All planned test cases executed
   - Performance thresholds met (if applicable)
   - Security scan results reviewed
4. Define **suspension criteria** — when to pause testing
5. Define **resumption criteria** — when to restart after suspension

## Output Format

```markdown
# Quality Gate: [Phase Name]

## Entry Criteria
- [ ] [Prerequisite 1]
- [ ] [Prerequisite 2]

## Exit Criteria
- [ ] [Completion criterion 1]
- [ ] [Completion criterion 2]

## Suspension Criteria
- [Condition that pauses testing]

## Resumption Criteria
- [Condition that allows restart]

## Metrics Thresholds
| Metric | Target | Current |
|--------|--------|---------|
| [Coverage] | [X%] | [—] |
| [Open critical defects] | [0] | [—] |
```
