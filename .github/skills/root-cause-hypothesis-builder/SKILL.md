---
name: root-cause-hypothesis-builder
description: 'Build root cause hypotheses for test failures by analyzing symptoms, context, and recent changes. Use when investigating why a test or system failed, building a structured investigation approach, narrowing down the likely cause of a defect, or systematically ruling out potential causes.'
---

# Root Cause Hypothesis Builder

Build structured root cause hypotheses for test failures to guide investigation.

## When to Use

- When investigating why a test or system component failed
- When building a systematic investigation approach
- When narrowing down the likely cause of a defect
- When multiple potential causes need to be evaluated

## Procedure

1. Gather context:
   - What failed (test, feature, system behavior)
   - When it started failing (after which change)
   - Failure pattern (always, intermittent, conditional)
   - Recent changes (code, config, dependencies, infrastructure)
   - Environment details (which environment, which run)
2. Build hypotheses by zone:
   - **Code change**: Recent code change introduced the bug
   - **Configuration**: Config change or drift caused the issue
   - **Data**: Test data, production data, or data state issue
   - **Dependency**: Library update, API change, or external service issue
   - **Infrastructure**: Environment, resource, or platform issue
   - **Test issue**: The test itself is wrong or brittle
3. For each hypothesis:
   - State the hypothesis clearly
   - List evidence for and against
   - Define how to verify (what to check)
   - Estimate likelihood
4. Recommend investigation order (most likely first)

## Output Format

```markdown
# Root Cause Analysis: [Failure Description]

## Context
- **Failed**: [What failed]
- **When**: [When it started]
- **Pattern**: [Always / Intermittent / Conditional]
- **Recent changes**: [What changed]

## Hypotheses (ranked by likelihood)

### Hypothesis 1: [Description]
- **Zone**: [Code / Config / Data / Dependency / Infra / Test]
- **Likelihood**: [High / Medium / Low]
- **Evidence for**: [Supporting evidence]
- **Evidence against**: [Contradicting evidence]
- **Verify by**: [Specific investigation steps]

## Recommended Investigation Order
1. [Most likely cause — check this first]
2. [Next most likely]
```
