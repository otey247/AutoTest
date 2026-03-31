# STRIDE Methodology — Detailed Guide

STRIDE is a threat classification framework developed at Microsoft. Each letter represents a category of threat that maps to a security property being violated.

| Category | Security Property Violated | Core Question |
|----------|---------------------------|---------------|
| **S**poofing | Authentication | Can an attacker pretend to be someone or something else? |
| **T**ampering | Integrity | Can an attacker modify data they should not? |
| **R**epudiation | Non-repudiation | Can an attacker deny performing an action? |
| **I**nformation Disclosure | Confidentiality | Can an attacker access data they should not see? |
| **D**enial of Service | Availability | Can an attacker prevent legitimate use of the system? |
| **E**levation of Privilege | Authorization | Can an attacker do something they are not allowed to do? |

---

## How to Apply STRIDE

### 1. Decompose the System

Build a data flow diagram (DFD) before starting STRIDE analysis. Use these four element types:

- **External entities** (rectangles): Users, browsers, third-party APIs, partner systems — anything outside your control.
- **Processes** (circles): Application code, services, serverless functions — anything that transforms data.
- **Data stores** (parallel lines): Databases, caches, file systems, message queues, object storage.
- **Data flows** (arrows): HTTP requests, database queries, message bus events, file reads/writes.

Mark **trust boundaries** with dashed lines wherever the trust level changes. Common trust boundaries:

- Internet ↔ DMZ
- DMZ ↔ Internal network
- Client-side ↔ Server-side
- Application tier ↔ Database tier
- Your service ↔ Third-party service
- Authenticated zone ↔ Unauthenticated zone

### 2. Enumerate Threats Per Element

Walk through each DFD element and apply the relevant STRIDE categories:

| DFD Element | Applicable STRIDE Categories |
|-------------|------------------------------|
| External entity | S, R |
| Process | S, T, R, I, D, E |
| Data store | T, R, I, D |
| Data flow | T, I, D |

### 3. Document and Prioritize

For every threat discovered, record it in the threat table with likelihood, impact, mitigation, and test action.

---

## Spoofing — Deep Dive

**Definition:** An attacker impersonates another user, service, or system component to gain unauthorized access.

### Questions to Ask

- How does each component authenticate the caller? Is it mutual?
- Can API keys, tokens, or session cookies be stolen and replayed?
- Are service-to-service calls authenticated (mTLS, signed JWTs, API keys)?
- Can DNS spoofing redirect traffic to an attacker-controlled server?
- Are there default or shared credentials anywhere in the system?
- Can an attacker register an account that impersonates a legitimate user (e.g., similar username, email alias)?

### Real-World Examples

1. **Session hijacking via cookie theft.** An XSS vulnerability allows an attacker to steal a session cookie and impersonate the victim. Mitigation: Set HttpOnly and Secure flags; implement Content Security Policy.
2. **Service impersonation in microservices.** Without mutual TLS, any pod in the cluster can call internal APIs pretending to be a trusted service. Mitigation: Enforce mTLS with a service mesh; validate service identity in JWT claims.
3. **OAuth token replay.** An access token stolen from a mobile app is replayed against the API from a different device. Mitigation: Bind tokens to device fingerprints; use short-lived tokens with refresh rotation.

---

## Tampering — Deep Dive

**Definition:** An attacker modifies data in transit, at rest, or in processing to alter system behavior.

### Questions to Ask

- Is data integrity verified when crossing trust boundaries (checksums, HMACs, digital signatures)?
- Can an attacker modify HTTP request parameters, headers, or body after leaving the client?
- Are database records protected against unauthorized modification?
- Can configuration files, environment variables, or deployment artifacts be altered?
- Are software updates and dependencies verified with signatures?
- Can log entries be modified or deleted to cover tracks?

### Real-World Examples

1. **Parameter tampering in e-commerce.** An attacker modifies the `price` field in a hidden form input or API request to pay less. Mitigation: Never trust client-supplied prices; recalculate server-side from the product catalog.
2. **Man-in-the-middle on API calls.** An attacker intercepts HTTP traffic between mobile app and backend, modifying request payloads. Mitigation: Enforce TLS everywhere; implement certificate pinning on mobile clients.
3. **Dependency poisoning.** A malicious package is published to a public registry with a name similar to an internal package. Mitigation: Use a private registry with namespace scoping; verify package checksums.

---

## Repudiation — Deep Dive

**Definition:** A user or attacker performs an action and later denies it, and the system cannot prove otherwise.

### Questions to Ask

- Are all security-relevant events logged (authentication, authorization, data changes, admin actions)?
- Do logs include who, what, when, and from where?
- Are logs stored in a tamper-evident, append-only system?
- Can a user claim "I never did that" and be right because no record exists?
- Are financial or contractual transactions digitally signed or otherwise non-repudiable?
- Are there separate audit logs independent of application logs?

### Real-World Examples

1. **Missing audit trail for admin actions.** A system administrator modifies user permissions, but no log records the change. When a breach occurs, the team cannot determine how the attacker gained access. Mitigation: Log all privilege changes to an immutable audit log with the admin's identity.
2. **Unsigned API transactions.** A partner claims they never sent a particular API request that triggered a financial transfer. Mitigation: Require request signing (HMAC or asymmetric signatures) on all partner API calls.
3. **Client-side-only logging.** Application logs are stored on the client device where the user can delete them. Mitigation: Send security events to a server-side logging service in real time.

---

## Information Disclosure — Deep Dive

**Definition:** Sensitive data is exposed to an unauthorized party through any channel.

### Questions to Ask

- Are error messages revealing stack traces, database schemas, or internal paths?
- Is PII or sensitive data logged in application or access logs?
- Is data encrypted in transit (TLS 1.2+) and at rest (AES-256 or equivalent)?
- Can an attacker enumerate users, resources, or internal structure through predictable IDs or timing differences?
- Are backups and exports encrypted and access-controlled?
- Do HTTP response headers leak server technology, version, or internal IPs?
- Can an attacker use side-channel attacks (timing, error codes) to infer secrets?

### Real-World Examples

1. **Verbose error pages in production.** A stack trace in an HTTP 500 response reveals the database connection string. Mitigation: Use generic error pages in production; log detailed errors server-side only.
2. **User enumeration via login form.** Different error messages for "user not found" vs "wrong password" allow attackers to discover valid accounts. Mitigation: Return a single generic message for all login failures.
3. **Unencrypted database backups on S3.** Database backups stored in a public S3 bucket without encryption. Mitigation: Enable server-side encryption; enforce bucket policies blocking public access.

---

## Denial of Service — Deep Dive

**Definition:** An attacker makes the system unavailable to legitimate users by exhausting resources.

### Questions to Ask

- Are there rate limits on all public-facing endpoints?
- Can an attacker trigger expensive operations with a single request (complex queries, large file processing)?
- Are there unbounded loops, allocations, or recursion paths?
- Can a regex pattern be exploited to cause catastrophic backtracking (ReDoS)?
- Are connection pools, thread pools, and memory limited and monitored?
- Can a single tenant consume all shared resources in a multi-tenant system?
- Is there protection against application-layer DDoS (slowloris, slow POST)?

### Real-World Examples

1. **ReDoS in input validation.** A crafted email address triggers catastrophic backtracking in a regex validator, consuming 100% CPU. Mitigation: Use non-backtracking regex engines or set match timeouts; validate input length first.
2. **Zip bomb via file upload.** A 42 KB zip file expands to 4.5 PB, exhausting disk and memory. Mitigation: Limit decompressed size; scan uploads before extraction; enforce file size quotas.
3. **Unbounded GraphQL query.** A deeply nested query consumes all server memory and database connections. Mitigation: Limit query depth, complexity, and result size; implement query cost analysis.

---

## Elevation of Privilege — Deep Dive

**Definition:** An attacker gains permissions or access beyond what was intended for their role.

### Questions to Ask

- Are authorization checks enforced on every request, not just in the UI?
- Can an attacker access another user's data by changing an ID in the URL or request body (IDOR)?
- Are there horizontal privilege escalation paths (user A accessing user B's resources)?
- Are there vertical privilege escalation paths (regular user gaining admin access)?
- Are default roles and permissions following least privilege?
- Can an attacker exploit race conditions to bypass authorization checks?
- Are JWT claims or session attributes validated on every request, not just at login?

### Real-World Examples

1. **IDOR in user profile API.** Changing `/api/users/123/profile` to `/api/users/456/profile` returns another user's data. Mitigation: Validate that the authenticated user owns the requested resource on every API call.
2. **Mass assignment / over-posting.** An attacker adds `"role": "admin"` to a user update request, and the server blindly accepts it. Mitigation: Use allowlists for bindable fields; never bind request data directly to internal models.
3. **JWT none algorithm attack.** An attacker changes the JWT header to `"alg": "none"` and removes the signature. A misconfigured library accepts the token. Mitigation: Explicitly configure accepted algorithms; reject unsigned tokens.

---

## Tips for Effective STRIDE Analysis

1. **Apply STRIDE per element, not per system.** Analyze each DFD element individually to avoid missing threats.
2. **Use the STRIDE-per-interaction variant** for complex systems: apply STRIDE to each data flow crossing a trust boundary rather than to each element.
3. **Do not skip "unlikely" threats.** Document them with a Low rating so they are tracked and revisited.
4. **Involve the development team.** Developers know the implementation details that reveal real attack paths.
5. **Time-box the exercise.** A focused two-hour session per feature is more effective than an exhaustive week-long review.
6. **Iterate.** Start with a high-level model, then decompose high-risk areas into detailed sub-models.
