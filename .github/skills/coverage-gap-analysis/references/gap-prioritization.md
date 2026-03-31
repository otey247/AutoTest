# Gap Prioritization Framework

Use this framework to decide which coverage gaps to close first.

## Priority Matrix

Score each gap on two axes, then multiply:

| | Low Effort (1) | Medium Effort (2) | High Effort (3) |
|---|---|---|---|
| **High Risk (3)** | Priority 1 (score 3) | Priority 1 (score 6) | Priority 2 (score 9) |
| **Medium Risk (2)** | Priority 1 (score 2) | Priority 2 (score 4) | Priority 3 (score 6) |
| **Low Risk (1)** | Priority 2 (score 1) | Priority 3 (score 2) | Priority 4 (score 3) |

Lower score = address first. When scores tie, prefer the higher-risk gap.

## Risk Scoring Criteria

| Factor | High (3) | Medium (2) | Low (1) |
|--------|----------|-----------|---------|
| Business impact of failure | Revenue loss, data breach, compliance violation | Degraded experience, workaround exists | Cosmetic, rare edge case |
| Frequency of use | Core workflow, daily use | Regular use, weekly | Rarely used |
| Change velocity | Frequently modified code | Occasional changes | Stable, mature code |
| Complexity | Many integrations, complex logic | Moderate logic | Simple, straightforward |

## Effort Scoring Criteria

| Factor | Low (1) | Medium (2) | High (3) |
|--------|---------|-----------|----------|
| Test creation | Simple unit test | Integration test with setup | E2E with environment + data |
| Dependencies | Self-contained | Needs stubs/mocks | Needs external services |
| Infrastructure | Existing test infra | Minor setup needed | New tooling required |

## Decision Process

1. List all identified gaps
2. Score each gap for risk (1–3) and effort (1–3)
3. Calculate priority score (risk × effort)
4. Sort by priority score ascending (lowest first)
5. Group into action tiers:
   - **Tier 1 — Do now**: Score 1–3, high risk or low effort
   - **Tier 2 — Plan next sprint**: Score 4–6
   - **Tier 3 — Backlog**: Score 6–9, low risk and high effort

## Quick Decision Rules

- Any gap in a **critical business flow** → Tier 1 regardless of effort
- Any gap that is a **known vulnerability** → Tier 1
- Any gap in **code being actively changed** → Bump up one tier
- Any gap in **deprecated or sunset code** → Bump down one tier or skip
