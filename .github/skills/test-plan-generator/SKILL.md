---
name: test-plan-generator
description: 'Generate structured test plans for features, sprints, or releases. Use when creating a test plan, defining test scope and approach, documenting a testing strategy for a feature or project, preparing test planning artifacts for team review, deciding what to test and how, writing a QA plan, or planning test coverage for an upcoming release or sprint.'
---

# Test Plan Generator

Generate structured test plans that define scope, approach, resources, schedule, entry/exit criteria, and deliverables. Produce plans scaled to the right level of detail — lightweight for a single feature, comprehensive for a full release.

## When to Use

- A new feature, sprint, or release needs a test plan
- You need to document test scope and approach for stakeholder review
- Preparing for test execution kickoff and need a shared agreement on what will be tested
- A project has unclear testing responsibilities and needs a written plan
- Onboarding QA to a feature and need a structured handoff artifact
- Planning regression or smoke coverage for a release candidate
- Risk-based testing decisions need to be documented and justified

## Procedure

Follow these steps in order. Adjust depth based on plan scope — a feature plan can be concise; a release plan requires more rigor. See [./references/templates.md](./references/templates.md) for ready-made templates at each level.

### Step 1: Gather Context

Collect all available inputs before writing anything:

- **Requirements**: User stories, acceptance criteria, PRDs, design docs
- **Architecture**: System diagrams, service boundaries, data flows, integration points
- **Change scope**: Files changed, components affected, blast radius of the change
- **Constraints**: Timeline, available environments, team capacity, tooling
- **History**: Past defects in this area, known flaky tests, previous test results

If information is missing, note it as a risk in Step 7 rather than blocking.

### Step 2: Define Scope

State explicitly what is in scope and out of scope. Be precise — vague scope is the top reason test plans fail.

- **In scope**: List features, user flows, integrations, and non-functional attributes to test
- **Out of scope**: List what will NOT be tested and why (e.g., unchanged modules, third-party SaaS, deferred features)
- **Assumptions**: State any assumptions that affect scope (e.g., "auth service is stable and will not be regression tested")

### Step 3: Assess Risk

Prioritize testing effort based on risk. Use a likelihood × impact assessment to rank areas. See [./references/risk-assessment.md](./references/risk-assessment.md) for the full framework.

- Identify high-risk areas: new code, complex logic, integrations, areas with defect history
- Identify low-risk areas: unchanged modules, well-tested utilities, read-only views
- Assign each area a risk rating (Critical / High / Medium / Low)
- Allocate more testing depth and earlier scheduling to higher-risk areas

### Step 4: Choose Test Levels and Types

Map each in-scope area to the appropriate test levels and types:

| Test Level | Typical Types | Use When |
|------------|--------------|----------|
| Unit | Functional, boundary, error-path | Testing isolated logic, calculations, transformations |
| Component | Integration within a module, contract | Testing a service or module with its direct collaborators |
| Integration | API, data flow, contract | Testing interactions between services or systems |
| System / E2E | User journey, workflow, regression | Validating complete business flows end-to-end |
| Non-functional | Performance, security, accessibility | Validating quality attributes beyond correctness |

Do not default to "test everything at every level." Match test level to the risk and nature of each area.

### Step 5: Define Test Approach

For each test level, specify:

- **Method**: Automated, manual, exploratory, or hybrid
- **Tooling**: Frameworks, runners, environments (e.g., Jest, Playwright, JMeter)
- **Ownership**: Who writes and executes (dev, QA, SDET, shared)
- **Data strategy**: How test data is created, seeded, or managed
- **Environment**: Which environment(s) each level runs in (local, CI, staging, pre-prod)

### Step 6: Set Entry and Exit Criteria

Define objective criteria so the team agrees on when testing starts and when it is done.

**Entry criteria** (testing can begin when):
- Code is merged to the test branch and builds are green
- Test environment is provisioned and accessible
- Test data is seeded or available
- Requirements and acceptance criteria are reviewed

**Exit criteria** (testing is complete when):
- All planned test cases are executed
- Critical and high-severity defects are resolved or have approved waivers
- Test coverage meets the agreed threshold (e.g., 80% branch coverage for unit tests)
- Regression suite passes with no new failures
- Stakeholder sign-off is obtained (for release-level plans)

### Step 7: Identify Risks and Mitigations

Document risks that could derail testing. For each risk, state the impact and a concrete mitigation:

- Environment instability → Mitigation: pre-book environment slots, have a fallback environment
- Incomplete requirements → Mitigation: timebox exploratory testing, flag gaps early
- Test data unavailability → Mitigation: create synthetic data generators, use anonymized production snapshots
- Team capacity constraints → Mitigation: prioritize by risk, defer low-risk manual testing

### Step 8: Define Schedule and Deliverables

- Map milestones to dates (or sprint boundaries)
- List deliverables: test plan document, test cases, automation scripts, defect reports, test summary report
- Define reporting cadence: daily standups, end-of-sprint summaries, release sign-off reports

## Inline Example

A condensed feature-level test plan (see [./examples/feature-test-plan.md](./examples/feature-test-plan.md) for a full example):

```markdown
# Test Plan: Shopping Cart — Apply Coupon Code

## Scope
- **In scope**: Coupon validation, discount calculation, cart total update, error messages for invalid coupons
- **Out of scope**: Payment processing (unchanged), product catalog (separate team)
- **Assumptions**: Coupon service API is deployed to staging

## Risk Assessment
| Area | Risk | Rating |
|------|------|--------|
| Discount calculation with stacking rules | Complex business logic, past defects | High |
| Coupon input UI | Simple form field, low complexity | Low |
| Coupon service integration | New external dependency | High |

## Test Approach
| Level | Types | Method | Tools |
|-------|-------|--------|-------|
| Unit | Discount calc, validation rules | Automated | Jest |
| Integration | Coupon service API contract | Automated | Supertest + Pact |
| E2E | Apply coupon flow, error scenarios | Automated + exploratory | Playwright |

## Entry Criteria
- Coupon service deployed to staging; API contract finalized
- Feature branch merged and CI green

## Exit Criteria
- All High-risk test cases pass; no Critical/High defects open
- Discount calculation edge cases verified (stacking, expiry, min-spend)

## Risks
| Risk | Impact | Mitigation |
|------|--------|------------|
| Coupon service staging instability | Blocks integration tests | Stub service for CI; run real integration in nightly |
| Stacking rules not fully specified | Gaps in test coverage | Exploratory session + sync with product |

## Schedule
| Milestone | Target |
|-----------|--------|
| Unit + integration tests written | Sprint day 3 |
| E2E automation complete | Sprint day 5 |
| Exploratory testing session | Sprint day 6 |
| Test summary report | Sprint day 7 |
```

## Scaling Test Plans by Context

Adjust the depth and formality of the plan to the scope of the work:

### Feature-Level Plan

- **Length**: 1–2 pages
- **Focus**: Scope, risk, test approach, exit criteria
- **Skip or minimize**: Detailed schedule, resource allocation, governance
- **Audience**: Dev team, QA, tech lead
- **Template**: See "Feature-Level Template" in [./references/templates.md](./references/templates.md)

### Sprint-Level Plan

- **Length**: 2–3 pages
- **Focus**: Aggregated scope across stories, shared environment needs, regression strategy
- **Include**: Cross-story dependencies, combined risk view, sprint exit criteria
- **Audience**: Scrum team, product owner
- **Template**: See "Sprint-Level Template" in [./references/templates.md](./references/templates.md)

### Release-Level Plan

- **Length**: 4–8 pages
- **Focus**: Full test strategy, environment plan, resource allocation, sign-off process
- **Include**: Regression pack scope, performance/security testing, rollback testing, go/no-go criteria
- **Audience**: QA lead, engineering managers, release manager, stakeholders
- **Template**: See "Release-Level Template" in [./references/templates.md](./references/templates.md)

## Common Pitfalls

Avoid these anti-patterns when generating test plans:

| Pitfall | Why It Hurts | What to Do Instead |
|---------|-------------|-------------------|
| **Scope says "test everything"** | No prioritization; team spreads thin and misses critical areas | Be explicit about in-scope items; rank by risk and cut low-value areas |
| **No risk analysis** | All areas get equal effort; high-risk areas are under-tested | Perform likelihood × impact assessment; allocate depth proportionally |
| **Missing exit criteria** | No objective definition of "done"; testing drags or stops prematurely | Define measurable exit criteria before testing begins |
| **Copy-pasted from last release** | Plan does not reflect current scope, risks, or architecture changes | Start from context; reuse templates, not stale content |
| **Plan written but never referenced** | Wasted effort; team does ad-hoc testing anyway | Keep plans short and actionable; review in sprint planning and standups |
| **No environment or data strategy** | Tests fail due to environment issues, not code defects | Document environment needs and data seeding steps explicitly |
| **Ignoring non-functional testing** | Performance, security, or accessibility issues found late | Include non-functional test types when the feature has quality-attribute risk |
| **One plan for all contexts** | Feature plans are bloated; release plans are too thin | Scale plan depth to context — see Scaling Test Plans section above |

## Related Skills

These skills complement `test-plan-generator` for broader test planning and execution:

| Skill | When to Use Together |
|-------|---------------------|
| `coverage-gap-analysis` | After writing the plan, verify that planned tests actually cover all requirements and code paths |
| `risk-based-prioritization` | When deciding which test cases to run first or which areas deserve deeper testing |
| `phase-test-matrix-generator` | When mapping which test types apply to each SDLC phase across the project |
| `regression-pack-curator` | When the plan includes regression testing and you need to select or trim the regression suite |
| `smoke-pack-generator` | When defining a quick smoke suite for post-deployment or environment verification |

## Reference Files

- [./references/templates.md](./references/templates.md) — Ready-made templates for feature, sprint, and release-level test plans
- [./references/risk-assessment.md](./references/risk-assessment.md) — Risk assessment framework with likelihood × impact matrices and categorization guidance
- [./examples/feature-test-plan.md](./examples/feature-test-plan.md) — Complete worked example of a feature-level test plan
