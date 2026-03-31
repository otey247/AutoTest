# Threat Model: ShopEasy E-Commerce Platform

## Metadata

- **Date**: 2025-01-15
- **Author**: Security Engineering Team
- **Version**: 1.0
- **Status**: In Review

## System Overview

ShopEasy is a web-based e-commerce platform that allows customers to browse products, manage a shopping cart, complete purchases with credit card payments, and track orders. The system consists of:

- **Web frontend** — Single-page application (React) served via CDN
- **API gateway** — Routes requests, enforces rate limits, terminates TLS
- **Auth service** — Handles registration, login, MFA, and JWT issuance
- **Product service** — Manages product catalog, search, and inventory
- **Cart service** — Manages shopping cart state per user session
- **Order service** — Orchestrates checkout, payment, and fulfillment
- **Payment gateway** — Third-party PCI-compliant payment processor (Stripe)
- **Notification service** — Sends email and SMS confirmations
- **PostgreSQL database** — Stores users, orders, products, and inventory
- **Redis cache** — Caches product catalog and session data
- **Object storage (S3)** — Stores product images and invoices
- **Message queue (RabbitMQ)** — Async communication between services

## Assets

| Asset | Sensitivity | Description |
|-------|------------|-------------|
| User credentials | Critical | Passwords (hashed), MFA secrets |
| Payment card data | Critical | Card numbers passed to payment gateway (not stored) |
| PII | High | Names, emails, addresses, phone numbers |
| Order history | High | Purchase records, amounts, delivery addresses |
| Session tokens | High | JWTs granting authenticated access |
| Product catalog | Low | Public product data, pricing, inventory counts |
| Application secrets | Critical | API keys, database credentials, JWT signing keys |

## Data Flow Diagram

```
                        {Trust Boundary: Internet / DMZ}
                                    |
[Browser/Mobile App] ---(1: HTTPS)---> (API Gateway)
                                         |
                          {Trust Boundary: DMZ / Internal}
                                         |
              +----------+----------+----+----+----------+
              |          |          |         |          |
         (2: Auth)  (3: Product) (4: Cart) (5: Order) (6: Notification)
              |          |          |         |          |
              +-----+----+----+----+---------+          |
                    |         |              |          |
              [PostgreSQL] [Redis]    (7: Payment GW)  |
                                    {Trust Boundary:    |
                                     Internal / 3rd     |
                                     Party}        [Email/SMS Provider]
                                                   {Trust Boundary:
                                                    Internal / 3rd Party}

Flow 1: Browser to API Gateway — HTTPS, JSON (credentials, product queries, cart ops, orders)
Flow 2: API Gateway to Auth Service — gRPC/TLS (login, token validation)
Flow 3: API Gateway to Product Service — gRPC/TLS (catalog queries, search)
Flow 4: API Gateway to Cart Service — gRPC/TLS (add/remove items, get cart)
Flow 5: API Gateway to Order Service — gRPC/TLS (checkout, order status)
Flow 6: Order Service to Notification Service — RabbitMQ/TLS (order events)
Flow 7: Order Service to Payment Gateway — HTTPS (payment tokenization, charge)
```

## Trust Boundaries

| ID | Boundary | Description |
|----|----------|-------------|
| TB1 | Internet / DMZ | Between end-user browsers and the API gateway |
| TB2 | DMZ / Internal network | Between the API gateway and internal microservices |
| TB3 | Internal / Payment gateway | Between order service and Stripe |
| TB4 | Internal / Notification providers | Between notification service and email/SMS APIs |
| TB5 | Application / Database | Between services and PostgreSQL/Redis |

## Assumptions and Scope Exclusions

- The payment gateway (Stripe) is PCI-DSS compliant; we do not store card numbers.
- CDN configuration and DDoS protection at the network edge are out of scope.
- Physical security of data centers is managed by the cloud provider and out of scope.
- Mobile app binary protection (jailbreak detection, obfuscation) is out of scope for this model.

## Threats

### Authentication and Session Management

| ID | Flow/Component | STRIDE | Threat | Likelihood | Impact | Risk | Mitigation | Test Action |
|----|---------------|--------|--------|-----------|--------|------|-----------|-------------|
| T01 | Flow 1 / Auth Service | S | Credential stuffing using leaked password databases | H | H | Critical | Rate-limit login to 5 attempts per minute per IP; require MFA for sensitive actions; monitor for credential reuse | Attempt 10 rapid logins; verify lockout triggers; confirm MFA prompt for payment |
| T02 | Auth Service | S | Session fixation — attacker sets a known session ID before victim authenticates | M | H | High | Regenerate session ID on authentication; invalidate old sessions | Authenticate with a pre-set session ID; verify a new ID is issued |
| T03 | Auth Service | I | JWT signing key exposure through config leak or error message | M | H | High | Store signing key in secret manager (AWS Secrets Manager); rotate keys quarterly | Review error responses for key material; verify key is not in env vars or config files |
| T04 | Flow 1 | I | Session token theft via XSS | M | H | High | Set HttpOnly, Secure, SameSite=Strict on cookies; enforce strict CSP | Inject script tags in all input fields; verify CSP blocks execution |
| T05 | Auth Service | R | No audit log for password changes and MFA enrollment | M | M | Medium | Log all credential changes with user ID, timestamp, and source IP | Change password; verify audit log entry exists in SIEM |

### Product and Cart Operations

| ID | Flow/Component | STRIDE | Threat | Likelihood | Impact | Risk | Mitigation | Test Action |
|----|---------------|--------|--------|-----------|--------|------|-----------|-------------|
| T06 | Product Service | T | Price tampering — attacker modifies price in API request | H | H | Critical | Ignore client-supplied prices; recalculate from product catalog server-side | Submit order with modified price in request body; verify server uses catalog price |
| T07 | Product Service / Redis | T | Cache poisoning — attacker manipulates cached product data | L | H | Medium | Validate cache entries on write; use signed cache keys; separate caches per data type | Inject modified product data into cache endpoint; verify it is rejected |
| T08 | Cart Service | T | Cart quantity manipulation to trigger negative prices or integer overflow | M | M | Medium | Validate quantity bounds (1–99); use unsigned integers; reject negative values | Submit cart update with quantity -1, 0, and MAX_INT; verify rejection |
| T09 | Flow 3 | D | Search query DoS — crafted search query causes expensive database scan | M | H | High | Limit query complexity; set query timeout; implement search pagination | Submit wildcard and regex searches; verify timeout and rate limit enforcement |
| T10 | Product Service | I | Inventory count leakage reveals business-sensitive stock data | L | L | Low | Return "in stock" / "out of stock" boolean instead of exact counts for public API | Call product API as unauthenticated user; verify exact count is not returned |

### Checkout and Payment

| ID | Flow/Component | STRIDE | Threat | Likelihood | Impact | Risk | Mitigation | Test Action |
|----|---------------|--------|--------|-----------|--------|------|-----------|-------------|
| T11 | Order Service | T | Race condition — concurrent checkout requests double-spend a discount code | M | H | High | Use database-level locking on discount redemption; idempotency keys on orders | Send 10 concurrent checkout requests with the same discount code; verify only one succeeds |
| T12 | Flow 7 | I | Payment data exposed in application logs | M | H | High | Never log card numbers or CVVs; use structured logging with field-level redaction | Trigger a payment error; inspect all log outputs for card data |
| T13 | Flow 7 | S | Attacker replays a legitimate payment confirmation to create fraudulent orders | M | H | High | Verify payment intent ID with Stripe on every order; use webhook signatures | Replay a captured Stripe webhook; verify the order service rejects the duplicate |
| T14 | Order Service | E | Broken function-level access — user can view or cancel other users' orders | H | H | Critical | Validate authenticated user owns the order on every endpoint | Request GET /orders/{other_user_id} and DELETE /orders/{other_user_id}; verify 403 |
| T15 | Order Service | R | No audit trail for order cancellations and refunds | M | M | Medium | Log all order state transitions with actor, timestamp, and reason | Cancel an order; verify audit log captures the event with full context |

### Data Storage

| ID | Flow/Component | STRIDE | Threat | Likelihood | Impact | Risk | Mitigation | Test Action |
|----|---------------|--------|--------|-----------|--------|------|-----------|-------------|
| T16 | PostgreSQL | T | SQL injection in search or filter parameters | H | H | Critical | Use parameterized queries exclusively; enable ORM query logging in test | Submit SQL payloads in search, filter, and sort parameters; verify no injection |
| T17 | PostgreSQL | I | Unencrypted database backups stored in S3 | M | H | High | Enable server-side encryption (SSE-S3) on backup bucket; restrict bucket policy | Verify S3 bucket encryption setting; attempt public access to backup bucket |
| T18 | Redis | I | Session data accessible without authentication on Redis | M | H | High | Enable Redis AUTH; use TLS for Redis connections; restrict network access | Attempt to connect to Redis without credentials; verify connection is refused |
| T19 | S3 | I | Product images or invoices accessible via predictable URLs | M | M | Medium | Use signed URLs with expiration for invoices; product images can remain public | Attempt to access an invoice URL without a valid signature; verify 403 |
| T20 | PostgreSQL | I | PII exposed through database query in error responses | M | H | High | Use generic error messages in production; log detailed errors server-side only | Trigger a database constraint violation; inspect API response for leaked PII |

### Infrastructure and Supply Chain

| ID | Flow/Component | STRIDE | Threat | Likelihood | Impact | Risk | Mitigation | Test Action |
|----|---------------|--------|--------|-----------|--------|------|-----------|-------------|
| T21 | API Gateway | D | Application-layer DDoS — high volume of legitimate-looking requests | M | H | High | WAF rules; rate limiting per user and IP; auto-scaling with cost caps | Load test at 10x expected traffic; verify rate limits and WAF blocks trigger |
| T22 | All Services | E | Compromised dependency introduces malicious code | M | H | High | Pin dependency versions; use lockfiles; run automated vulnerability scans; use private registry | Run dependency audit; verify no known vulnerabilities; check lockfile integrity |
| T23 | CI/CD Pipeline | T | Attacker modifies deployment pipeline to inject malicious code | L | H | Medium | Require signed commits; branch protection; restrict pipeline secret access; review pipeline changes | Attempt to push unsigned commit to main; verify it is rejected |
| T24 | All Services | I | Application secrets (API keys, DB passwords) exposed in code or config | M | H | High | Use secret manager; scan repos with secret detection tools; rotate secrets on exposure | Run secret scanning tool; verify no secrets in source code or container images |
| T25 | Notification Service | S | Email spoofing — attacker sends emails appearing to come from ShopEasy | M | M | Medium | Configure SPF, DKIM, and DMARC on sending domain | Send test email; verify SPF/DKIM/DMARC records pass validation |

### Cross-Cutting

| ID | Flow/Component | STRIDE | Threat | Likelihood | Impact | Risk | Mitigation | Test Action |
|----|---------------|--------|--------|-----------|--------|------|-----------|-------------|
| T26 | All Services | I | Excessive logging of PII (names, emails, addresses) in application logs | M | M | Medium | Implement structured logging with PII field redaction; classify log fields | Generate orders; inspect logs for PII; verify redaction |
| T27 | All Services | R | Insufficient monitoring delays breach detection | M | H | High | Centralized log aggregation; alerting on anomalies (failed logins, error spikes, unusual access patterns) | Simulate attack patterns; verify alerts fire within SLA |
| T28 | All Endpoints | S | Account takeover via password reset flow — attacker triggers reset and intercepts email | M | H | High | Rate-limit password reset; use short-lived tokens; notify user on email change | Request password reset; verify token expiration; attempt reuse |

## Priority Recommendations

Ranked by risk score (Critical first):

1. **T01 — Credential stuffing** (Critical): Implement rate limiting and mandatory MFA. Owner: Auth team. Target: Sprint 12.
2. **T06 — Price tampering** (Critical): Enforce server-side price recalculation. Owner: Product team. Target: Sprint 12.
3. **T14 — Broken access control on orders** (Critical): Add ownership validation to all order endpoints. Owner: Order team. Target: Sprint 12.
4. **T16 — SQL injection** (Critical): Audit all database queries for parameterization. Owner: All teams. Target: Sprint 12.
5. **T03 — JWT key exposure** (High): Migrate signing keys to AWS Secrets Manager. Owner: Platform team. Target: Sprint 13.
6. **T04 — XSS / session theft** (High): Deploy strict CSP; audit all output encoding. Owner: Frontend team. Target: Sprint 13.
7. **T11 — Checkout race condition** (High): Implement database-level locking and idempotency keys. Owner: Order team. Target: Sprint 13.
8. **T12 — Payment data in logs** (High): Implement field-level log redaction. Owner: Platform team. Target: Sprint 13.
9. **T17 — Unencrypted backups** (High): Enable S3 encryption on all backup buckets. Owner: Infrastructure team. Target: Sprint 12.
10. **T18 — Redis without auth** (High): Enable Redis AUTH and TLS. Owner: Infrastructure team. Target: Sprint 12.

## Open Questions

1. Should we implement device fingerprinting for session binding (T04), or is MFA sufficient?
2. What is the acceptable rate limit for login attempts across all entry points (web, mobile, API)?
3. Do we need a separate threat model for the admin dashboard, which has elevated privileges?
4. Should invoice PDFs in S3 be encrypted with customer-specific keys for multi-tenant isolation?
5. How frequently should dependency vulnerability scans run — daily, on every PR, or both?
