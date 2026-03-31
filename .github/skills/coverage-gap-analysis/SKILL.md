---
name: coverage-gap-analysis
description: 'Identify gaps in test coverage by comparing tested areas against requirements, code changes, and risk profiles. Use when checking for untested requirements, finding uncovered code paths, evaluating whether testing is sufficient for release, or identifying areas that need additional test attention.'
---

# Coverage Gap Analysis

Identify gaps in test coverage by comparing what is tested against what should be tested across multiple coverage dimensions: requirements, code, risk, configuration, and test-type.

## When to Use

- When checking whether all requirements have test coverage
- When evaluating if testing is sufficient for release
- When code changes need coverage verification
- When risk areas need coverage assessment
- After a sprint or phase to verify nothing was missed
- Before go-live to confirm all critical paths are tested
- When onboarding a new module or service with existing tests
- When a production incident reveals a testing blind spot

## Coverage Dimensions

A thorough gap analysis examines five dimensions. See `./references/coverage-types.md` for measurement approaches.

| Dimension | What It Measures | Key Question |
|-----------|-----------------|--------------|
| **Requirement coverage** | Tests mapped to requirements/stories | Does every requirement have at least one test? |
| **Code coverage** | Lines, branches, paths executed by tests | Which code paths are never exercised? |
| **Risk coverage** | Test depth proportional to risk level | Are high-risk areas tested more deeply? |
| **Configuration coverage** | Environments, browsers, data variants tested | Are all supported configurations verified? |
| **Test-type coverage** | Presence of each needed test type (unit, integration, security, performance, accessibility) | Are any entire categories of testing missing? |

## Procedure

### Step 1 — Define Scope and Goals

- Identify what you are analyzing: a feature, module, release, or the full system.
- State the coverage goal (e.g., "all P1 requirements must have at least one automated test").
- Agree on which dimensions matter for this analysis.

### Step 2 — Inventory What Exists

- Collect the test inventory: automated tests, manual test cases, exploratory charters.
- Gather requirements, user stories, and acceptance criteria.
- Obtain code coverage reports if available (line, branch, path).
- List known risk areas, recent incidents, and high-change modules.

### Step 3 — Map Tests to Coverage Targets

For each dimension, create a mapping:

- **Requirements**: For every requirement or acceptance criterion, list the test(s) that verify it.
- **Code**: Use coverage tooling to identify untested lines, branches, and functions.
- **Risk**: For each identified risk, confirm proportional test depth exists.
- **Configuration**: List supported environments and data variants; mark which are tested.
- **Test-type**: List needed test types per component; mark which exist.

### Step 4 — Identify Gaps

Walk through each mapping and flag entries with no coverage or insufficient coverage:

- A requirement with zero tests is a **full gap**.
- A requirement with one happy-path test but no negative/edge tests is a **partial gap**.
- A high-risk module with only unit tests but no integration or security tests is a **type gap**.
- A feature tested only on Chrome but required on Safari/Firefox is a **configuration gap**.

### Step 5 — Prioritize Gaps

Use the framework in `./references/gap-prioritization.md` to rank gaps by:

1. **Business risk** — What is the impact if this gap hides a defect?
2. **Likelihood** — How likely is a defect in this area (change frequency, complexity)?
3. **Effort to close** — How much work is needed to add the missing coverage?
4. **Regulatory or compliance need** — Is coverage mandated?

Assign each gap a priority: Critical, High, Medium, or Low.

### Step 6 — Recommend Actions

For each gap, specify:

- What test(s) to add or expand
- Who should own the work
- Target completion date or sprint
- Whether the gap blocks release

## Inline Example — Payment Module

Below is a condensed gap analysis for a payment processing module. See `./examples/payment-module-gap-analysis.md` for the full version.

```
Module: PaymentService (checkout, refunds, webhooks)

Requirement Coverage: 18/22 requirements have tests (82%)
  GAPS:
  - REQ-PAY-19: Partial refund for multi-item orders — NO tests
  - REQ-PAY-20: Currency conversion rounding — NO tests
  - REQ-PAY-21: Webhook retry on 5xx — only happy-path test
  - REQ-PAY-22: PCI audit logging — NO tests

Code Coverage: 74% line, 61% branch
  GAPS:
  - refund_calculator.py: 42% branch (error paths untested)
  - webhook_handler.py: retry loop never exercised
  - currency_converter.py: rounding edge cases uncovered

Risk Coverage:
  GAPS:
  - HIGH RISK: No integration test for payment gateway timeout
  - HIGH RISK: No security test for injection in payment metadata

Test-Type Coverage:
  GAPS:
  - No performance tests for checkout under load
  - No accessibility tests for payment form

Priority Actions:
  1. [CRITICAL] Add tests for partial refund logic (REQ-PAY-19)
  2. [CRITICAL] Add payment gateway timeout integration test
  3. [HIGH] Add PCI audit logging verification (REQ-PAY-22)
  4. [HIGH] Add currency rounding boundary tests (REQ-PAY-20)
  5. [MEDIUM] Add webhook retry tests for 5xx responses
  6. [MEDIUM] Add checkout performance test
```

## Common Pitfalls

| Pitfall | Why It Matters | Mitigation |
|---------|---------------|------------|
| **Equating code coverage with test quality** | 100% line coverage can still miss logic errors if assertions are weak or edge cases are absent. | Combine code coverage with requirement coverage and assertion quality checks. |
| **Ignoring non-functional gaps** | Teams focus on functional tests and forget performance, security, accessibility, and usability. | Always include test-type coverage as a dimension. |
| **Analysis paralysis** | Trying to catalog every possible gap delays actionable results. | Time-box the analysis; focus on high-risk areas first and iterate. |
| **Stale traceability** | Requirements-to-test mappings rot when stories change but maps are not updated. | Automate traceability where possible; review mappings each sprint. |
| **Treating all gaps equally** | Not all gaps carry the same risk; fixing low-risk gaps first wastes effort. | Always prioritize by business risk and likelihood. |
| **Single-dimension analysis** | Looking only at code coverage misses entire categories of missing tests. | Analyze all five dimensions listed above. |

## Output Format

```markdown
# Coverage Gap Analysis — [Module/Feature/Release]

## Scope
- Analysis target: [description]
- Dimensions analyzed: [requirements, code, risk, configuration, test-type]
- Date: [YYYY-MM-DD]

## Summary
- Overall requirement coverage: [X/Y] ([Z%])
- Code coverage: [line%] line, [branch%] branch
- Critical gaps: [N]
- High-priority gaps: [N]
- Release-blocking gaps: [N]

## Gaps by Dimension

### Requirement Gaps
| ID | Requirement | Gap Type | Risk | Recommended Action |
|----|------------|----------|------|--------------------|

### Code Coverage Gaps
| Module/File | Line % | Branch % | Key Uncovered Paths | Priority |
|-------------|--------|----------|---------------------|----------|

### Risk Coverage Gaps
| Risk Area | Expected Depth | Actual Depth | Gap | Priority |
|-----------|---------------|-------------|-----|----------|

### Configuration Gaps
| Configuration | Tested? | Gap Description | Priority |
|--------------|---------|----------------|----------|

### Test-Type Gaps
| Component | Missing Test Type | Impact | Priority |
|-----------|------------------|--------|----------|

## Prioritized Action Plan
| # | Gap | Priority | Owner | Target Date | Blocks Release? |
|---|-----|----------|-------|-------------|-----------------|

## Residual Risk
[Describe any accepted gaps and their justification]
```

## Reference Materials

- `./references/coverage-types.md` — Guide to coverage dimensions and measurement approaches
- `./references/gap-prioritization.md` — Framework for prioritizing gaps by risk, effort, and impact

## Related Skills

- **traceability-matrix-builder** — Build the requirement-to-test mapping that feeds requirement coverage analysis
- **branch-coverage-gap-finder** — Deep-dive into untested code branches and paths
- **risk-based-prioritization** — Prioritize testing effort based on risk assessment
- **test-plan-generator** — Generate test plans that address identified coverage gaps
- **regression-pack-curator** — Curate regression test suites informed by gap analysis results
