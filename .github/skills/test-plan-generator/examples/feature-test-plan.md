# Test Plan: E-Commerce Checkout — Guest Checkout Flow

**Author**: QA Lead
**Date**: 2025-01-15
**Status**: Approved

---

## 1. Overview

The guest checkout flow allows users to complete a purchase without creating an account. This feature adds a new "Continue as Guest" option on the login/registration gate, collects shipping and payment information in a streamlined form, and processes the order through the existing order service.

### Related Stories

- SHOP-401: Guest checkout entry point on login gate
- SHOP-402: Shipping address form with address validation
- SHOP-403: Payment form with credit card and PayPal support
- SHOP-404: Order confirmation page and email
- SHOP-405: Guest order lookup by email + order number

---

## 2. Scope

### In Scope

- **Guest checkout entry point**: "Continue as Guest" button on the login/registration page
- **Shipping address form**: Address input, validation via address verification API, shipping method selection
- **Payment processing**: Credit card (via Stripe), PayPal redirect flow, payment error handling
- **Order creation**: Order submitted to order service, inventory reserved, confirmation number generated
- **Order confirmation**: Confirmation page display, confirmation email sent via email service
- **Guest order lookup**: Search by email + order number on the order status page
- **Cart preservation**: Cart contents persist through the guest checkout flow
- **Mobile responsiveness**: All checkout steps render correctly on mobile viewports (375px–428px)

### Out of Scope

- **Registered user checkout**: Unchanged flow; covered by existing regression suite
- **Product catalog and search**: Owned by the catalog team; no changes in this release
- **Inventory management backend**: Only the reservation API call is tested; backend inventory logic is out of scope
- **Shipping carrier integration**: Uses existing shipping service; no changes
- **Tax calculation**: Uses existing tax service; no changes

### Assumptions

- Stripe test environment is available and configured with test API keys
- PayPal sandbox is available for redirect flow testing
- Address verification API (Smarty Streets) sandbox is provisioned
- Email service (SendGrid) test environment is configured to capture emails without sending to real addresses
- Order service API contract (v2.3) is finalized and deployed to staging

---

## 3. Risk Assessment

| # | Area | Category | Likelihood | Impact | Rating | Testing Response |
|---|------|----------|-----------|--------|--------|-----------------|
| 1 | Payment processing (Stripe + PayPal) | Integration | Medium | High | **High** | Contract tests for Stripe API. E2E tests for both payment methods including error flows. Manual verification of PayPal redirect in sandbox. |
| 2 | Address validation with edge cases | Functional | Medium | Medium | **Medium** | Unit tests for validation logic. Integration test with Smarty Streets sandbox. Boundary tests for international addresses and PO boxes. |
| 3 | Order confirmation email delivery | Integration | Medium | Medium | **Medium** | Integration test verifying email service is called with correct payload. Manual verification of email rendering in test inbox. |
| 4 | Cart preservation across checkout steps | Data | High | High | **Critical** | Unit tests for cart state management. E2E tests verifying cart contents at each step. Test browser back/forward navigation. Test session timeout. |
| 5 | Guest order lookup | Functional | Low | Low | **Low** | Unit test for lookup logic. One E2E happy-path test. |
| 6 | Mobile responsiveness | Functional | Medium | Medium | **Medium** | Visual regression tests at 375px and 428px. Manual exploratory on iOS Safari and Android Chrome. |
| 7 | Concurrent checkout (same item, low stock) | Data | Medium | High | **High** | Load test with concurrent checkouts for a low-stock item. Verify inventory reservation and conflict handling. |

---

## 4. Test Approach

### 4.1 Test Levels

| Test Level | Scope | Method | Tools | Environment | Owner |
|------------|-------|--------|-------|-------------|-------|
| Unit | Validation logic, cart state, discount calc, form helpers | Automated (CI) | Jest + React Testing Library | CI | Dev team |
| Component | Checkout form components (shipping, payment, confirmation) | Automated (CI) | Jest + React Testing Library | CI | Dev team |
| Integration | Stripe API, PayPal API, address verification, email service, order service | Automated (CI + nightly) | Supertest + Pact | CI + Staging | SDET |
| E2E | Full guest checkout journey (happy path + error flows) | Automated + exploratory | Playwright | Staging | QA |
| Performance | Concurrent checkout under load | Automated | k6 | Perf env | SDET |
| Accessibility | Checkout forms WCAG 2.1 AA compliance | Automated + manual | axe-core + manual audit | Staging | QA |

### 4.2 Test Data Strategy

| Data Type | Strategy | Details |
|-----------|----------|---------|
| Products | Seeded fixtures | 5 products with varying stock levels (1, 5, 100, 1000, 0) |
| Addresses | Hardcoded test addresses | Valid US, valid international, invalid, PO box, military (APO/FPO) |
| Credit cards | Stripe test card numbers | `4242424242424242` (success), `4000000000000002` (decline), `4000000000009995` (insufficient funds) |
| PayPal | Sandbox buyer accounts | Pre-configured sandbox buyer with funded balance |
| Coupons | Seeded in coupon service | One valid percentage coupon, one expired, one minimum-spend |

### 4.3 Test Environments

| Environment | Purpose | URL | Notes |
|-------------|---------|-----|-------|
| CI | Unit, component, integration (stubbed) | N/A (pipeline only) | All external services stubbed |
| Staging | E2E, integration (real), accessibility | `https://staging.example.com` | Real Stripe test mode, PayPal sandbox, Smarty Streets sandbox |
| Perf | Performance / load testing | `https://perf.example.com` | Isolated environment with production-like data volume |

---

## 5. Test Cases — High-Level Summary

### 5.1 Guest Checkout Happy Path

| ID | Scenario | Level | Priority |
|----|----------|-------|----------|
| TC-001 | User clicks "Continue as Guest" and proceeds to shipping form | E2E | High |
| TC-002 | User enters valid US shipping address and selects standard shipping | E2E | High |
| TC-003 | User enters credit card, payment succeeds, order is created | E2E | High |
| TC-004 | User sees confirmation page with order number | E2E | High |
| TC-005 | User receives confirmation email with order details | Integration | High |
| TC-006 | User looks up order by email + order number on order status page | E2E | Medium |

### 5.2 Payment Error Flows

| ID | Scenario | Level | Priority |
|----|----------|-------|----------|
| TC-010 | Credit card declined — user sees error message and can retry | E2E | High |
| TC-011 | Insufficient funds — user sees specific error and can use different card | E2E | High |
| TC-012 | Stripe API timeout — user sees friendly error, order is not created | Integration | High |
| TC-013 | PayPal redirect — user completes payment on PayPal and returns | E2E | High |
| TC-014 | PayPal redirect — user cancels on PayPal and returns to payment step | E2E | High |
| TC-015 | PayPal API error — user sees error message and can select different payment | E2E | Medium |

### 5.3 Address Validation

| ID | Scenario | Level | Priority |
|----|----------|-------|----------|
| TC-020 | Valid US address — passes validation, user proceeds | Unit + E2E | High |
| TC-021 | Invalid address — user sees suggestion from address verification API | Integration | Medium |
| TC-022 | International address — validation accepts supported countries | Unit | Medium |
| TC-023 | PO box — handled per shipping method rules | Unit | Low |
| TC-024 | Address verification API timeout — graceful degradation, user can proceed | Integration | Medium |

### 5.4 Cart Preservation

| ID | Scenario | Level | Priority |
|----|----------|-------|----------|
| TC-030 | Cart contents visible on checkout summary throughout all steps | E2E | Critical |
| TC-031 | Browser back button from payment to shipping preserves cart | E2E | High |
| TC-032 | Session timeout during checkout — cart restored on return | E2E | High |
| TC-033 | Item goes out of stock during checkout — user notified before payment | Integration | High |
| TC-034 | Price change during checkout — user sees updated total before payment | Integration | Medium |

### 5.5 Mobile and Accessibility

| ID | Scenario | Level | Priority |
|----|----------|-------|----------|
| TC-040 | Checkout forms usable at 375px viewport width | E2E (mobile) | Medium |
| TC-041 | All form fields have accessible labels and error announcements | Accessibility | Medium |
| TC-042 | Keyboard navigation through entire checkout flow | Accessibility | Medium |
| TC-043 | Screen reader announces step progress and errors | Accessibility | Medium |

### 5.6 Performance

| ID | Scenario | Level | Priority |
|----|----------|-------|----------|
| TC-050 | 100 concurrent guest checkouts — all orders created, no duplicates | Performance | High |
| TC-051 | 50 concurrent checkouts for item with stock of 10 — correct inventory conflict handling | Performance | High |
| TC-052 | Checkout page load time under 2 seconds at p95 under normal load | Performance | Medium |

---

## 6. Entry Criteria

- [x] All stories (SHOP-401 through SHOP-405) code-complete and merged to `release/guest-checkout`
- [x] CI pipeline green (unit + component + integration with stubs)
- [x] Staging environment deployed with guest checkout build
- [x] Stripe test mode and PayPal sandbox configured
- [x] Address verification sandbox provisioned
- [x] Email service test environment capturing to test inbox
- [x] Test data seeded (products, coupons, test addresses)

---

## 7. Exit Criteria

- [ ] All Critical and High priority test cases pass
- [ ] Zero open Critical defects
- [ ] All High defects resolved or explicitly waived by product owner with documented justification
- [ ] Medium defects triaged — fix or defer decision documented
- [ ] Performance targets met: p95 checkout latency < 2s, zero duplicate orders under concurrent load
- [ ] Accessibility audit passes WCAG 2.1 AA for all checkout pages
- [ ] Confirmation email renders correctly in Gmail, Outlook, and Apple Mail
- [ ] Product owner completes UAT walkthrough and signs off

---

## 8. Risks and Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Stripe sandbox rate limits during load testing | Medium | High | Run load tests during off-peak hours; use Stripe mock for CI-level load tests |
| PayPal sandbox instability | Medium | Medium | Maintain a stubbed PayPal integration for CI; run real PayPal tests in nightly, not blocking |
| Address verification API changes schema | Low | Medium | Contract test pinned to current schema version; alert on schema drift |
| Staging environment shared with other teams | High | Medium | Reserve dedicated staging slot for checkout testing days 4–7; communicate schedule |
| Guest checkout session handling edge cases | Medium | High | Dedicated exploratory session for session timeout, multi-tab, and browser back/forward scenarios |

---

## 9. Schedule

| Milestone | Target Date | Owner | Status |
|-----------|------------|-------|--------|
| Test plan reviewed and approved | Jan 15 | QA Lead | Done |
| Unit + component tests complete | Jan 17 | Dev team | In progress |
| Integration tests (Stripe, PayPal, address, email) | Jan 20 | SDET | Not started |
| E2E automation (happy path + error flows) | Jan 22 | QA | Not started |
| Performance tests (concurrent checkout) | Jan 23 | SDET | Not started |
| Accessibility audit | Jan 23 | QA | Not started |
| Exploratory testing session | Jan 24 | QA | Not started |
| UAT walkthrough with product owner | Jan 27 | Product Owner | Not started |
| Test summary report and go/no-go recommendation | Jan 28 | QA Lead | Not started |

---

## 10. Deliverables

- [x] This test plan
- [ ] Automated test suite (unit, component, integration, E2E) in `tests/guest-checkout/`
- [ ] Performance test scripts in `perf/guest-checkout/`
- [ ] Accessibility audit report
- [ ] Defect reports in JIRA (tagged `guest-checkout`)
- [ ] Exploratory testing session notes
- [ ] Test summary report with go/no-go recommendation
