---
name: defect-report-writer
description: 'Write clear, actionable defect reports with reproduction steps, expected vs actual behavior, and supporting evidence. Use when documenting bugs found during testing, creating defect reports for developers, improving poorly written bug reports, or standardizing defect reporting across the team.'
---

# Defect Report Writer

Write clear, actionable defect reports with reproduction steps and supporting evidence.

## When to Use

- When documenting bugs found during testing
- When creating defect reports for development teams
- When improving poorly written bug reports
- When standardizing defect reporting format

## Procedure

1. Capture essential defect information:
   - What happened (actual behavior)
   - What should have happened (expected behavior)
   - How to reproduce it (step-by-step)
   - When it was found (which test, which phase)
   - Where it occurs (environment, browser, device)
2. Assess severity and priority:
   - Severity: Impact of the defect (Critical, High, Medium, Low)
   - Priority: Urgency of the fix (P1, P2, P3, P4)
3. Gather supporting evidence:
   - Screenshots or screen recordings
   - Log snippets
   - Network trace data
   - Test data used
4. Write the report in a clear, developer-friendly format
5. Suggest initial investigation starting points

## Output Format

```markdown
# Defect Report

## Summary
**Title**: [Short, descriptive title]
**Severity**: [Critical / High / Medium / Low]
**Priority**: [P1 / P2 / P3 / P4]
**Component**: [Affected component]
**Found in**: [Version/Environment]
**Found by**: [Tester/Test name]

## Description
**Expected behavior**: [What should happen]
**Actual behavior**: [What actually happens]

## Steps to Reproduce
1. [Precondition]
2. [Step 1]
3. [Step 2]
4. [Step 3]
5. **Result**: [What you observe]

## Environment
- Browser/Device: [details]
- OS: [details]
- Environment: [dev/staging/production]
- Test data: [data used]

## Supporting Evidence
- [Screenshots, logs, network traces]

## Investigation Hints
- [Where to look first]
- [Related code area]
- [Similar past defects]
```
