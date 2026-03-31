# Coverage Gap Analysis: Payment Processing Module

## Summary

- **Overall coverage**: 62%
- **Critical gaps**: 4
- **High-priority gaps**: 6
- **Scope**: Payment service — checkout, charge, refund, webhook handling

## Requirement Gaps

| Requirement | Risk Level | Gap Description | Recommended Action |
|------------|-----------|----------------|-------------------|
| REQ-PAY-07: Partial refunds | High | No test for partial refund + remaining balance calculation | Add unit + integration tests |
| REQ-PAY-12: Currency conversion | Medium | Only tested USD→EUR; 8 other supported pairs untested | Add parameterized tests for all pairs |
| REQ-PAY-15: Payment retry on timeout | Critical | No test for retry logic or idempotency key handling | Add integration test with simulated timeout |
| REQ-PAY-18: PCI audit logging | Critical | Audit log assertions missing from all payment tests | Add log verification to existing tests |

## Code Coverage Gaps

| Module | Current Coverage | Target | Gap | Priority |
|--------|-----------------|--------|-----|----------|
| `PaymentProcessor.charge()` | 85% | 90% | Error branches for declined cards | High |
| `RefundService.processRefund()` | 45% | 80% | Partial refund path, concurrent refund guard | Critical |
| `WebhookHandler.handle()` | 30% | 75% | Signature verification failure, duplicate event, unknown event type | Critical |
| `CurrencyConverter.convert()` | 70% | 85% | Rounding edge cases, stale rate fallback | Medium |

## Risk Coverage Gaps

| Risk Area | Expected Coverage | Actual Coverage | Priority |
|-----------|------------------|----------------|----------|
| Payment data at rest | Encryption unit + integration tests | No encryption tests | Critical |
| Stripe webhook replay | Idempotency integration test | No replay test | High |
| Race condition: double charge | Concurrent integration test | No concurrency test | High |
| Network timeout to payment provider | Retry + circuit breaker test | Retry logic untested | High |
| PCI compliance logging | Audit log assertions | No log assertions | Critical |

## Test-Type Gaps

| Test Type | Current State | Gap |
|-----------|-------------|-----|
| Unit | Good coverage on happy paths | Missing error/boundary cases |
| Integration | Basic charge flow only | No refund, webhook, retry flows |
| Contract | None | No Stripe API contract tests |
| Performance | None | No load test for checkout endpoint |
| Security | None | No input validation or injection tests on payment forms |

## Recommended Actions

| Priority | Gap | Action | Effort |
|----------|-----|--------|--------|
| 1 | PCI audit logging | Add log assertions to existing payment tests | Low |
| 1 | Payment retry on timeout | Create integration test with mock timeout | Medium |
| 1 | Webhook handler coverage | Add tests for signature failure, duplicates, unknown events | Medium |
| 2 | Partial refund logic | Unit + integration tests for partial refund flow | Medium |
| 2 | Double-charge race condition | Concurrent integration test | High |
| 2 | Stripe contract tests | Implement Pact consumer contracts for Stripe API | High |
| 3 | Currency conversion edge cases | Parameterized tests for all currency pairs + rounding | Low |
| 3 | Checkout performance baseline | k6 load test for POST /checkout | Medium |
| 3 | Payment form input validation | SQL injection + XSS tests on payment form fields | Medium |
