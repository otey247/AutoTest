---
name: e2e-scenario-generator
description: 'Generate end-to-end test scenarios that cover complete business workflows across multiple systems. Use when designing e2e test suites, creating user journey tests, validating cross-system business flows, building comprehensive acceptance test scenarios, or prioritizing scenario coverage by business value and risk.'
---

# E2E Scenario Generator

Generate end-to-end test scenarios that validate complete business workflows from the
user's first action through the final system state, spanning UI, API, database, and
external service boundaries.

## When to Use

- Designing end-to-end test suites for a new release or feature launch
- Creating user journey tests that span multiple pages or services
- Validating cross-system business flows (front-end → API → database → third-party)
- Building acceptance test scenarios for stakeholder sign-off
- Prioritizing which workflows to automate first based on risk and value
- Replacing or supplementing manual regression packs with automated e2e coverage

## Procedure

### Step 1 — Identify Workflows

Gather the business workflows that the e2e suite must cover.

1. List every core user journey: sign-up, login, purchase, account management,
   password reset, subscription renewal, etc.
2. List critical business processes: order fulfillment, payment processing,
   inventory sync, notification dispatch, report generation.
3. Map cross-system flows by drawing a sequence from the user action through
   every system boundary (UI → BFF → microservice → database → external API).
4. Consult product owners, support tickets, and analytics to confirm which
   journeys carry the highest traffic and revenue impact.

### Step 2 — Design Scenarios for Each Workflow

For every workflow identified in Step 1, create scenarios across four categories.

| Category | What to cover |
|---|---|
| **Happy path** | The most common successful flow end to end |
| **Alternative paths** | Valid variations — different payment methods, optional steps skipped, feature flags toggled |
| **Error / recovery paths** | Mid-flow failures — network timeout after payment, session expiry, duplicate submission |
| **Edge cases** | Unusual but valid combinations — max-length inputs, zero-quantity cart, timezone boundaries |

Keep each scenario focused on **one logical journey**. If a scenario branches into
two independent outcomes, split it into two scenarios.

### Step 3 — Define Verification Points

For every scenario step, define what to verify and how.

1. **UI verification** — Assert visible text, element state, navigation target.
   Prefer data-testid or ARIA role selectors over CSS class or XPath.
2. **API verification** — Assert response status, key payload fields, headers
   (e.g., cache-control, correlation ID).
3. **Data verification** — Assert database rows, message queue events, or cache
   entries created or updated by the step.
4. **Side-effect verification** — Assert emails sent, webhooks fired, audit logs
   written, third-party API calls made.

Place verification points at **every system boundary crossing** and at the final
end state.

### Step 4 — Specify Preconditions and Data Setup

For each scenario, document:

- **Test data** — Users, products, configurations needed before the test starts.
- **Environment state** — Feature flags, service stubs, clock overrides.
- **Setup method** — API seeding, database fixtures, or UI-driven setup.
- **Teardown method** — How to reset state so the next run starts clean.

Prefer API-based setup over UI-driven setup for speed and reliability.

### Step 5 — Prioritize Scenarios

Rank every scenario using a value-and-risk matrix.

| | High business value | Low business value |
|---|---|---|
| **High failure risk** | **Critical** — automate first | **High** — automate soon |
| **Low failure risk** | **Medium** — automate next | **Low** — automate last or keep manual |

Assign each scenario a priority label: Critical, High, Medium, or Low.

Additional prioritization factors:

- **Revenue impact** — Flows that directly generate or protect revenue rank higher.
- **User traffic** — High-traffic paths rank higher than admin-only paths.
- **Defect history** — Workflows with past production incidents rank higher.
- **Regulatory / compliance** — Flows under audit or legal obligation rank higher.
- **Change frequency** — Code areas that change often benefit more from automation.

### Step 6 — Choose a Framework and Structure Tests

Select a framework that fits the application type and team skills. See
[./references/frameworks.md](./references/frameworks.md) for detailed patterns.

| Framework | Best for |
|---|---|
| **Playwright** | Modern web apps, cross-browser, API + UI in one test |
| **Cypress** | Single-page apps, fast feedback, component + e2e |
| **Selenium WebDriver** | Multi-language teams, legacy browser support |
| **TestCafe** | Quick setup, no WebDriver dependency |

Structure the test suite by workflow, not by page:

```
tests/
  e2e/
    checkout/
      happy-path.spec.ts
      payment-failure.spec.ts
      guest-checkout.spec.ts
    registration/
      standard-signup.spec.ts
      social-login.spec.ts
```

For deeper scenario design techniques — user journey mapping, business workflow
analysis, and happy/sad/edge path identification — see
[./references/scenario-design.md](./references/scenario-design.md).

For a complete worked example, see
[./examples/ecommerce-checkout.md](./examples/ecommerce-checkout.md).

---

## Inline Example — User Registration and Onboarding

Below is a condensed e2e scenario for a registration-plus-onboarding flow.

```markdown
# E2E Scenarios: User Registration + Onboarding

## Scenario 1: New user completes registration and onboarding
- **Priority**: Critical
- **Preconditions**:
  - No account exists for test email `newuser@example.com`
  - Email service stub configured to capture outbound mail
- **Steps**:
  1. Navigate to /register → Verify: registration form is visible
  2. Fill name, email, password and submit → Verify: "Check your email" message appears
  3. Retrieve verification link from email stub → Verify: link contains valid token
  4. Open verification link → Verify: redirected to /onboarding, account status is "verified" in DB
  5. Complete onboarding wizard (select preferences, upload avatar) → Verify: preferences saved via API, avatar stored in object storage
  6. Click "Go to Dashboard" → Verify: dashboard loads with welcome banner, user profile shows selected preferences
- **Final state**: User record active, preferences persisted, welcome email sent
- **Systems involved**: Web UI, Auth API, Email Service, Object Storage, PostgreSQL
- **Estimated duration**: ~45 seconds automated

## Scenario 2: Registration with duplicate email
- **Priority**: High
- **Preconditions**:
  - Account already exists for `existing@example.com`
- **Steps**:
  1. Navigate to /register → Verify: registration form is visible
  2. Fill name, duplicate email, password and submit → Verify: inline error "Email already registered" appears, no new DB row created, no email sent
- **Final state**: No new account created, existing account unchanged
- **Systems involved**: Web UI, Auth API, PostgreSQL
- **Estimated duration**: ~10 seconds automated
```

---

## Common Pitfalls

### 1. Testing too many paths in one scenario

Combining happy path, error recovery, and edge cases in a single test makes
failures hard to diagnose and increases maintenance cost. Keep each scenario
focused on one logical journey.

### 2. Brittle selectors

Avoid CSS classes, deeply nested XPaths, or auto-generated IDs that change on
every build. Prefer `data-testid` attributes, ARIA roles, or accessible labels.

### 3. Missing data setup and teardown

Tests that rely on leftover data from previous runs create order-dependent
suites that fail on first run or in parallel. Always seed required data at
the start and clean up at the end — or use isolated test accounts.

### 4. Ignoring environment differences

Hardcoded URLs, credentials, or feature flags that differ between environments
cause false failures. Externalize configuration and validate it at test start.

### 5. Asserting too early or too late

Skipping intermediate verification points hides where a flow actually broke.
Inserting too many assertions slows execution. Place assertions at each
system boundary crossing and at the final state — no more, no less.

### 6. No timeout or retry strategy

Network latency and async processing cause flaky tests. Define explicit
wait conditions (not fixed sleeps) for every async step. Use retry logic
for known non-deterministic operations.

### 7. Giant catch-all suites without prioritization

Running every scenario on every commit wastes CI time. Tag scenarios by
priority and run Critical/High on every PR, Medium on nightly, Low on
weekly or release-candidate builds.

---

## Output Format

```markdown
# E2E Scenarios: [Workflow Name]

## Scenario N: [Descriptive Name]
- **Priority**: [Critical / High / Medium / Low]
- **Preconditions**: [Setup required — test data, environment state, stubs]
- **Data setup**: [API seed / DB fixture / UI-driven]
- **Steps**:
  1. [User action] → Verify: [expected result]
  2. [User action] → Verify: [expected result]
  3. [User action] → Verify: [expected result]
- **Final state**: [Expected end state across all systems]
- **Systems involved**: [List of systems touched]
- **Data teardown**: [Cleanup method]
- **Estimated duration**: [Time]
- **Tags**: [smoke, regression, nightly, release]
```

---

## Related Skills

| Skill | When to use alongside this one |
|---|---|
| **business-process-test-builder** | Model the underlying business rules before generating e2e scenarios |
| **smoke-pack-generator** | Extract a minimal smoke subset from the full e2e suite for fast CI gates |
| **regression-pack-curator** | Select which e2e scenarios to include in a regression run after a code change |
| **uat-pack-generator** | Convert e2e scenarios into user-acceptance-test scripts for stakeholder review |
| **state-transition-test-builder** | Identify state transitions within a workflow that need dedicated scenario coverage |

---

## Bundled Resources

- [./references/frameworks.md](./references/frameworks.md) — E2E testing framework patterns for Playwright, Cypress, Selenium, and TestCafe
- [./references/scenario-design.md](./references/scenario-design.md) — Scenario design techniques: user journey mapping, business workflow analysis, happy/sad/edge path identification
- [./examples/ecommerce-checkout.md](./examples/ecommerce-checkout.md) — Complete e2e scenario suite for an e-commerce checkout flow
