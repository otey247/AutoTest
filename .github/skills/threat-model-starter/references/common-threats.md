# Common Threats by Application Layer

A catalog of frequently encountered threats organized by the layer where they manifest. Use this as a checklist when performing threat modeling to ensure no major category is overlooked.

---

## Presentation Layer

Threats targeting the user interface, client-side code, and browser interactions.

| Threat | STRIDE | Description | Typical Mitigation |
|--------|--------|-------------|-------------------|
| Cross-Site Scripting (XSS) | T, I | Attacker injects malicious scripts into pages viewed by other users | Output encoding; Content Security Policy; HttpOnly cookies |
| Cross-Site Request Forgery (CSRF) | S | Attacker tricks an authenticated user into performing unintended actions | Anti-CSRF tokens; SameSite cookie attribute |
| Clickjacking | S, T | Attacker overlays a transparent iframe to capture user clicks on a different page | X-Frame-Options header; frame-ancestors CSP directive |
| Open redirect | S | Attacker manipulates a redirect URL to send users to a malicious site | Validate redirect targets against an allowlist |
| Client-side data exposure | I | Sensitive data stored in localStorage, sessionStorage, or cookies without protection | Avoid storing secrets client-side; use Secure and HttpOnly flags |
| DOM manipulation | T, E | Attacker modifies client-side DOM to bypass validation or alter displayed data | Never rely solely on client-side validation; enforce server-side checks |
| Insecure deep links / URL schemes | S, E | Mobile deep links allow attacker apps to intercept or spoof navigation | Verify app links with digital asset links; validate intent origins |

---

## Business Logic Layer

Threats targeting application logic, workflows, and rules enforcement.

| Threat | STRIDE | Description | Typical Mitigation |
|--------|--------|-------------|-------------------|
| Broken access control (IDOR) | E, I | Attacker accesses other users' resources by manipulating identifiers | Validate ownership on every request; use indirect references |
| Business logic bypass | T, E | Attacker skips steps in a workflow (e.g., payment without checkout) | Enforce state machine transitions server-side; validate preconditions |
| Race conditions / TOCTOU | T, E | Concurrent requests exploit timing gaps in check-then-act sequences | Use atomic operations; database-level locking; idempotency keys |
| Price / quantity manipulation | T | Attacker modifies prices, discounts, or quantities in requests | Recalculate all monetary values server-side from trusted sources |
| Insufficient rate limiting | D | Attacker floods an endpoint to brute-force credentials or exhaust resources | Implement rate limiting per user, IP, and endpoint |
| Mass assignment | E | Attacker sends extra fields in a request to modify protected attributes | Use allowlists for bindable fields; DTO mapping |
| Insecure direct object references | I, E | Predictable IDs allow enumeration and unauthorized access | Use UUIDs; enforce authorization checks on every data access |
| Broken function-level authorization | E | Attacker calls admin endpoints directly without proper role checks | Enforce role checks on every API endpoint; deny by default |

---

## Data Layer

Threats targeting databases, file systems, caches, and data at rest.

| Threat | STRIDE | Description | Typical Mitigation |
|--------|--------|-------------|-------------------|
| SQL injection | T, I, E | Attacker inserts SQL commands through unvalidated input | Parameterized queries; ORM with bound parameters; input validation |
| NoSQL injection | T, I, E | Attacker manipulates NoSQL query operators through user input | Input type validation; avoid passing raw objects to queries |
| Unencrypted data at rest | I | Sensitive data stored without encryption is exposed if storage is compromised | Encrypt with AES-256; manage keys via KMS; encrypt backups |
| Insufficient backup security | I, T | Backups stored without encryption or access control are exposed | Encrypt backups; restrict access; test restore procedures |
| Cache poisoning | T, I | Attacker injects malicious content into shared caches | Validate cache keys; use Vary headers; separate caches per user |
| Log injection | T, R | Attacker injects fake log entries to confuse forensics or exploit log viewers | Sanitize log inputs; use structured logging; encode special characters |
| Data retention violations | I | Retaining data longer than necessary increases breach impact | Implement retention policies; automate deletion; minimize data collection |
| Orphaned data | I | Data left behind after account deletion or migration | Implement cascading deletes; audit for orphaned records |

---

## Infrastructure Layer

Threats targeting servers, networks, containers, cloud resources, and deployment pipelines.

| Threat | STRIDE | Description | Typical Mitigation |
|--------|--------|-------------|-------------------|
| Unpatched vulnerabilities | E, T | Known CVEs in OS, runtime, or libraries are exploited | Automate patching; use vulnerability scanners; pin and update dependencies |
| Misconfigured cloud resources | I, E | Public S3 buckets, open security groups, overly permissive IAM roles | Infrastructure as code with security linting; least-privilege IAM |
| Container escape | E | Attacker breaks out of a container to access the host | Run as non-root; use read-only filesystems; enable seccomp/AppArmor |
| Supply chain attack | T, E | Malicious code introduced through compromised dependencies or build tools | Verify checksums; use lockfiles; scan dependencies; use private registries |
| DNS hijacking | S | Attacker redirects DNS to serve malicious content or intercept traffic | DNSSEC; monitor DNS records; use registrar lock |
| TLS misconfiguration | I, S | Weak ciphers, expired certificates, or missing HSTS allow interception | Enforce TLS 1.2+; automate certificate renewal; enable HSTS |
| Secrets in source code | I | API keys, passwords, or tokens committed to version control | Use secret managers; scan repositories with secret detection tools |
| Insecure CI/CD pipeline | T, E | Attacker modifies build scripts or injects code during deployment | Sign commits; protect pipeline secrets; restrict pipeline permissions |
| Insufficient monitoring | R, D | Lack of alerting delays detection of attacks and outages | Implement centralized logging; set up alerts for anomalies; monitor SLOs |
| Server-side request forgery (SSRF) | I, E | Attacker induces the server to make requests to internal resources | Validate and allowlist outbound URLs; block access to metadata endpoints |

---

## Cross-Cutting Threats

Threats that span multiple layers and require coordinated mitigations.

| Threat | STRIDE | Description | Typical Mitigation |
|--------|--------|-------------|-------------------|
| Insider threat | S, T, I, E | Malicious or negligent employee abuses authorized access | Least privilege; separation of duties; audit logging; access reviews |
| Social engineering | S | Attacker manipulates people to reveal credentials or perform actions | Security awareness training; phishing simulations; MFA |
| Account takeover | S, E | Attacker gains control of a user account through credential theft or session hijacking | MFA; credential breach monitoring; session binding; anomaly detection |
| Insufficient incident response | R | Organization cannot detect, contain, or recover from a breach effectively | Incident response plan; runbooks; tabletop exercises; retros |
| Compliance violations | I, R | Failure to meet regulatory requirements (GDPR, PCI-DSS, HIPAA) | Map controls to regulations; automate compliance checks; maintain evidence |
| Third-party integration risk | S, T, I | External services introduce vulnerabilities or data exposure | Vet third parties; limit data sharing; monitor API usage; have fallback plans |

---

## How to Use This Catalog

1. **During threat modeling**, walk through each layer and check whether the listed threats apply to your system.
2. **Mark each threat** as applicable, not applicable, or already mitigated.
3. **Add applicable threats** to your threat model table with specific details for your system.
4. **Do not treat this as exhaustive.** Your system may have domain-specific threats not listed here. Use this catalog as a starting point, not a ceiling.
