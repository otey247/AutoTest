---
name: risk-based-prioritization
description: 'Prioritize testing effort based on business impact and failure likelihood. Use when deciding which areas need the deepest test coverage, when planning test effort allocation, when triaging what to test first under time pressure, or when building a risk-based test strategy.'
---

# Risk-Based Prioritization

Prioritize testing effort by ranking features and areas by business impact and failure likelihood.

## When to Use

- When deciding which areas need the deepest test coverage
- When time is limited and testing effort must be focused
- When building a risk-based test strategy
- When new features need risk assessment for test planning

## Procedure

1. List the features, components, or areas under consideration
2. For each item, assess two dimensions:
   - **Business Impact** (if it fails): Critical / High / Medium / Low
     - Consider: revenue impact, user impact, regulatory impact, reputation
   - **Failure Likelihood**: High / Medium / Low
     - Consider: complexity, change frequency, dependency count, team familiarity, historical defect rate
3. Create a risk matrix mapping impact × likelihood
4. Assign test depth based on risk level:
   - **Critical risk** (High impact × High likelihood): Exhaustive testing — all paths, boundaries, negative cases
   - **High risk**: Broad coverage — positive, negative, key boundaries
   - **Medium risk**: Standard coverage — positive paths and major negative cases
   - **Low risk**: Minimal coverage — smoke-level or sample-based testing
5. Recommend test types appropriate for each risk level

## Output Format

```markdown
# Risk-Based Test Prioritization

## Risk Matrix

| Area | Business Impact | Failure Likelihood | Risk Level | Test Depth |
|------|----------------|-------------------|------------|------------|
| [Area] | [Critical/High/Med/Low] | [High/Med/Low] | [Critical/High/Med/Low] | [Exhaustive/Broad/Standard/Minimal] |

## Prioritized Test Plan
1. [Highest risk area] — [recommended test approach]
2. [Next highest] — [recommended test approach]
...

## Rationale
- [Key reasoning for top priorities]
```
