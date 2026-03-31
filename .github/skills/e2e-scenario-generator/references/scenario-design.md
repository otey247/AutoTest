# Scenario Design Techniques

Techniques for identifying, structuring, and refining end-to-end test scenarios
that cover real user behavior and business-critical workflows.

---

## 1. User Journey Mapping

A user journey map traces every interaction a user has with the system from their
first touchpoint to their final goal. It serves as the primary input for e2e
scenario design.

### How to build a user journey map

1. **Define the persona** — Identify the user type (new customer, returning
   buyer, admin, API consumer). Each persona may follow a different path.
2. **Set the goal** — State the outcome the user wants: "Purchase a product",
   "Reset password", "Generate a monthly report".
3. **List every touchpoint** — Walk through the flow action by action:
   page visited, button clicked, form submitted, email received, redirect
   followed, API called.
4. **Mark system boundaries** — Note where the flow crosses from one system
   to another (UI → API gateway → payment processor → email service).
5. **Annotate emotional state** — Flag points where the user is likely
   frustrated (long waits, confusing forms) or delighted (instant feedback,
   clear confirmation). These are high-value verification points.
6. **Identify drop-off points** — Analytics or support data may reveal where
   users abandon the flow. These points deserve dedicated error-path scenarios.

### Example — user journey map for e-commerce purchase

```
Persona: First-time buyer
Goal: Purchase a product and receive order confirmation

1. [Web UI] Land on homepage from ad link
2. [Web UI] Search for product → Verify: results appear
3. [Web UI] Select product → Verify: PDP loads with price and stock status
4. [Web UI] Add to cart → Verify: cart badge updates
5. [Web UI] Proceed to checkout → Verify: checkout page loads
6. [Web UI → Auth API] Create account or checkout as guest
7. [Web UI → Address API] Enter shipping address → Verify: address validated
8. [Web UI → Payment API] Enter payment details and submit
9. [Payment API → Payment Processor] Authorize payment → Verify: auth success
10. [Order API → DB] Create order record → Verify: order ID generated
11. [Notification Service → Email] Send confirmation email → Verify: email received
12. [Web UI] Display order confirmation page → Verify: order ID matches
```

---

## 2. Business Workflow Analysis

Business workflow analysis focuses on the **system's perspective** rather than
the user's. It reveals back-end processes, integrations, and state transitions
that a user journey map might miss.

### How to perform business workflow analysis

1. **Obtain process documentation** — Collect sequence diagrams, BPMN flows,
   runbooks, or architecture decision records.
2. **Identify actors** — List every system, service, and human role involved.
3. **Trace data flow** — Follow the data from input to final persistence:
   which services create, transform, validate, and store it.
4. **Find decision points** — Locate conditionals (if payment fails, if
   inventory is zero, if fraud score exceeds threshold). Each branch is a
   candidate scenario.
5. **Identify asynchronous steps** — Queued jobs, event-driven processing,
   and scheduled tasks need specific wait/assertion strategies.
6. **Map failure modes** — For every integration point, ask: "What happens if
   this service is slow, returns an error, or is completely down?"

### Decision-point extraction template

| Decision point | Condition A (default) | Condition B (alternative) | Condition C (error) |
|---|---|---|---|
| Payment authorization | Authorized → continue | Declined → show retry | Timeout → show error, hold order |
| Inventory check | In stock → allocate | Low stock → show warning | Out of stock → remove from cart |
| Address validation | Valid → continue | Ambiguous → show suggestions | Invalid → show inline error |
| Fraud check | Pass → continue | Review → hold order | Fail → cancel order, notify admin |

Each cell in the table becomes a potential e2e scenario or scenario variation.

---

## 3. Happy / Sad / Edge Path Identification

Categorize scenarios into three groups to ensure balanced coverage.

### Happy path

The single most common successful flow. There should be exactly **one** happy
path per workflow. It represents the default, expected behavior.

**Rules for happy-path scenarios**:
- Use the most common input values (not boundary values).
- Follow the default option at every decision point.
- Assert success at every verification point.
- This scenario should run on every PR / commit.

### Sad paths (error and failure paths)

Scenarios where something goes wrong but the system handles it gracefully.
These test the product's resilience and error messaging.

**Common sad-path categories**:

| Category | Examples |
|---|---|
| Validation failure | Invalid email, password too short, missing required field |
| Business rule rejection | Expired coupon, insufficient funds, item out of stock |
| Authentication / authorization | Wrong password, expired session, forbidden role |
| External service failure | Payment gateway down, email service timeout, API 5xx |
| Concurrency conflict | Double submission, concurrent edits, race condition |

**Rules for sad-path scenarios**:
- Test one failure mode per scenario.
- Assert the correct error message and that no partial side effects persist
  (no orphan records, no partial charges).
- Include at least one sad-path scenario for every external integration.

### Edge paths (boundary and corner cases)

Unusual but valid conditions that stress the system's handling of limits.

**Common edge-path categories**:

| Category | Examples |
|---|---|
| Boundary values | Max cart size, longest allowed name, zero-quantity line item |
| Unusual sequences | Back-button after submit, refresh mid-checkout, deep-link into step 3 |
| Timing | Action at session-expiry boundary, order placed at midnight UTC |
| Data combinations | Multi-currency cart, mixed digital + physical items, international address |
| Accessibility paths | Keyboard-only navigation through checkout, screen-reader flow |

**Rules for edge-path scenarios**:
- Prioritize edges that have caused production incidents before.
- Run edge-path scenarios on nightly or weekly cadence, not on every PR.

---

## 4. Scenario Decomposition

Large workflows often need to be decomposed into smaller, independent scenarios
that can run in parallel and fail in isolation.

### Decomposition guidelines

1. **One assertion theme per scenario** — A scenario tests either "payment
   succeeds" or "payment fails", never both.
2. **Independent data** — Each scenario creates its own test data and does not
   depend on data left by another scenario.
3. **Shared setup via fixtures** — Common setup (authenticated user, seeded
   catalog) belongs in shared fixtures, not duplicated in each scenario.
4. **Maximum 10-12 steps** — If a scenario exceeds 12 steps, look for a
   natural split point at a system boundary or user decision.
5. **Tagging for selective execution** — Tag scenarios with `@smoke`,
   `@regression`, `@nightly`, `@critical` so CI pipelines can run subsets.

### Decomposition example — checkout workflow

Instead of one monolithic "checkout" test:

| Scenario | Scope | Tag |
|---|---|---|
| Add single item to cart | Browse → cart | @smoke |
| Add multiple items and update quantities | Cart management | @regression |
| Apply valid coupon code | Cart → discount | @regression |
| Apply expired coupon code | Cart → error | @regression |
| Complete checkout with credit card | Cart → order confirmation | @smoke |
| Complete checkout with PayPal | Cart → order confirmation | @regression |
| Checkout with invalid card | Cart → payment error | @regression |
| Guest checkout | Cart → order confirmation | @regression |
| Checkout with saved address | Cart → order confirmation | @nightly |

---

## 5. Verification Point Placement

Deciding **where** to assert is as important as deciding **what** to assert.

### Placement rules

- **After every user-visible state change** — Page navigation, modal open/close,
  form submission feedback.
- **At every system boundary** — When the flow crosses from UI to API, from API
  to database, or from service to external provider.
- **At the final end state** — Always assert the ultimate outcome (order created,
  email sent, record persisted).
- **Skip intermediate UI-only transitions** — If a loading spinner appears for
  200ms, do not assert it; wait for the final state instead.

### Verification point checklist

For each verification point, answer:

1. **What** is being verified? (visible text, HTTP status, DB row, event)
2. **How** is it verified? (DOM assertion, API call, DB query, message queue read)
3. **When** can it be verified? (immediately, after async delay, after polling)
4. **What is the timeout?** (explicit wait duration before declaring failure)
5. **What does failure mean?** (which system or component is likely broken)

---

## 6. Data Strategy for E2E Tests

Test data management is the most common source of flaky e2e tests. Follow these
principles.

| Principle | Rationale |
|---|---|
| Create data per test run | Avoids collision between parallel runs |
| Use API or DB seeding, not UI | Faster and more reliable than clicking through forms |
| Generate unique identifiers | Append timestamps or UUIDs to emails and usernames |
| Clean up after each test | Prevents data accumulation and state leakage |
| Never depend on production data | Production data changes unpredictably and may contain PII |
| Externalize configuration | Base URLs, credentials, and feature flags belong in env vars or config files |
