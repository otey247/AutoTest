---
name: retest-scope-generator
description: 'Define the scope of retesting needed after a defect fix to verify the fix and check for regressions. Use when determining what to retest after a bug fix, defining the minimum safe retest scope, balancing thoroughness with efficiency in retesting, or creating retest plans for hotfixes.'
---

# Retest Scope Generator

Define the scope of retesting needed after a defect fix to verify the fix and catch regressions.

## When to Use

- When determining what to retest after a bug fix
- When defining the minimum safe retest scope
- When creating retest plans for hotfixes
- When balancing thoroughness with efficiency in retesting

## Procedure

1. Understand the fix:
   - What was changed (files, functions, configuration)
   - What was the original defect
   - How was it fixed (logic change, data fix, config change)
2. Define retest scope in concentric rings:
   - **Ring 1 — Confirmation**: Rerun the exact failing scenario to confirm the fix
   - **Ring 2 — Adjacent**: Test immediately related functionality
   - **Ring 3 — Regression**: Test broader areas that could be impacted
   - **Ring 4 — Smoke**: Run critical path smoke tests
3. For each ring, specify:
   - Which tests to run
   - Manual vs automated
   - Expected duration
4. Define the minimum acceptable scope (Rings 1-2) vs recommended scope (Rings 1-4)

## Output Format

```markdown
# Retest Scope: [Fix Description]

## Fix Details
- **Defect**: [Original issue]
- **Fix**: [What was changed]
- **Files changed**: [List]

## Retest Rings

### Ring 1: Confirmation (Required)
- [ ] [Exact scenario that originally failed]

### Ring 2: Adjacent (Required)
- [ ] [Related functionality test 1]
- [ ] [Related functionality test 2]

### Ring 3: Regression (Recommended)
- [ ] [Broader area test 1]
- [ ] [Broader area test 2]

### Ring 4: Smoke (Recommended)
- [ ] [Critical path smoke tests]

## Effort Estimate
- Minimum scope (Rings 1-2): [X hours]
- Recommended scope (Rings 1-4): [X hours]
```
