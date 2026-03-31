---
name: threat-model-starter
description: 'Create initial threat models by examining design and data flows to identify security risks. Use when starting threat modeling for a new feature, identifying trust boundaries and attack surfaces, generating STRIDE-based threat analysis, preparing security requirements before implementation, or updating models after architecture changes.'
---

# Threat Model Starter

Create initial threat models by examining design, data flows, and trust boundaries to identify security risks early in the development lifecycle.

## When to Use

- A new feature, service, or system needs threat analysis before implementation
- Identifying trust boundaries, entry points, and attack surfaces for an existing system
- Preparing security requirements and acceptance criteria before coding begins
- Updating threat models after architecture changes, new integrations, or incident response
- Onboarding a team to security thinking for a project they own
- Reviewing a pull request that introduces new data flows or external dependencies

## Procedure

### Step 1: Define Scope and Assets

Identify what is being protected and set the analysis boundary.

1. List the system components under analysis (services, databases, queues, CDNs, third-party APIs).
2. Identify valuable assets: user credentials, PII, payment data, session tokens, business-critical records.
3. State assumptions and out-of-scope items explicitly so reviewers know what was excluded.

### Step 2: Map Data Flows

Build a data flow diagram (DFD) covering how data enters, moves through, and exits the system.

1. Identify **entry points** — every place external data enters the system:
   - User inputs (forms, query parameters, headers, cookies)
   - API endpoints (REST, GraphQL, gRPC, WebSocket)
   - File uploads, message queues, webhook receivers
   - Scheduled jobs consuming external data
2. Identify **trust boundaries** — lines where trust level changes:
   - Internet to DMZ, DMZ to internal network
   - Client to server, server to database
   - Authenticated zone to unauthenticated zone
   - Between microservices with different privilege levels
   - Between your code and third-party libraries or services
3. Identify **data stores** — databases, caches, file systems, object storage, logs, backups.
4. Identify **external dependencies** — third-party APIs, identity providers, CDN, DNS, package registries.
5. Draw or describe flows using this notation:

```
[External Entity] --> (Process) --> [Data Store]
                         |
                   {Trust Boundary}
```

Refer to [./references/stride.md](./references/stride.md) for detailed DFD guidance.

### Step 3: Apply STRIDE Analysis

Walk through each component, data flow, and trust boundary. For every element, ask the questions below. See [./references/stride.md](./references/stride.md) for deeper guidance and real-world examples per category.

#### Spoofing (Authentication threats)

- Can an attacker impersonate a legitimate user, service, or component?
- Are credentials transmitted and stored securely?
- Is mutual authentication enforced where required (e.g., service-to-service)?
- Can API keys or tokens be reused from another context?

#### Tampering (Integrity threats)

- Can data be modified in transit between components?
- Can data at rest be altered without detection (database records, config files, logs)?
- Are checksums or signatures verified on inputs from external sources?
- Can request parameters be manipulated to change business logic outcomes?

#### Repudiation (Accountability threats)

- Are security-relevant actions logged with sufficient detail (who, what, when, from where)?
- Can a user deny performing a transaction because no audit trail exists?
- Are logs stored in a tamper-evident manner?
- Do administrative actions have independent audit records?

#### Information Disclosure (Confidentiality threats)

- Can sensitive data leak through error messages, logs, or HTTP headers?
- Is data encrypted in transit (TLS) and at rest?
- Are access controls enforced on every data retrieval path, including exports and backups?
- Can timing or side-channel attacks reveal information?

#### Denial of Service (Availability threats)

- Can an attacker exhaust resources (CPU, memory, disk, connections, rate limits)?
- Are there unbounded operations (uncontrolled pagination, unrestricted file uploads)?
- Can dependency failures cascade and take down the system?
- Is there protection against algorithmic complexity attacks (regex DoS, hash flooding)?

#### Elevation of Privilege (Authorization threats)

- Can a user access resources or actions beyond their role?
- Are authorization checks enforced at every layer, not just the UI?
- Can an attacker manipulate tokens, cookies, or hidden fields to gain admin access?
- Are default accounts, roles, or permissions overly permissive?

### Step 4: Rate Each Threat

Assign likelihood and impact using a simple High / Medium / Low scale.

| Rating | Likelihood criteria | Impact criteria |
|--------|-------------------|-----------------|
| **High** | Easily exploitable, public-facing, known attack patterns exist | Data breach, financial loss, regulatory penalty, full system compromise |
| **Medium** | Requires insider access or chained exploits | Partial data exposure, service degradation, limited blast radius |
| **Low** | Requires physical access or highly unlikely conditions | Minimal data affected, no customer impact, quick recovery |

Compute a **risk score** as Likelihood x Impact. Prioritize High-High and High-Medium threats first.

### Step 5: Recommend Mitigations and Test Actions

For each threat, propose:

1. A **mitigation** — the defensive control to reduce likelihood or impact.
2. A **test action** — a concrete, verifiable check that the mitigation works.

Map high-priority mitigations to security requirements or acceptance criteria that can flow into development tasks.

## Inline Example: Simple Web Application

A login page that accepts username/password and returns a JWT.

**Data flow:**

```
[Browser] --HTTPS--> (Login API) --SQL--> [User DB]
                         |
                   {Trust Boundary: Internet / Server}
```

**Threats identified:**

| ID | Component | STRIDE | Threat | Likelihood | Impact | Mitigation | Test Action |
|----|-----------|--------|--------|-----------|--------|-----------|-------------|
| T1 | Login API | S | Credential stuffing with leaked passwords | H | H | Rate-limit login attempts; require MFA | Verify lockout after 5 failed attempts |
| T2 | Login API | T | SQL injection in username field | H | H | Use parameterized queries | Submit `' OR 1=1 --` and confirm rejection |
| T3 | Login API | R | No log of failed login attempts | M | M | Log all authentication events to SIEM | Check audit log entries after failed login |
| T4 | Login API | I | JWT secret leaked in error response | M | H | Never include secrets in responses; use generic error messages | Trigger error and inspect response body |
| T5 | HTTPS | I | Downgrade to HTTP exposes credentials | M | H | Enforce HSTS; redirect HTTP to HTTPS | Attempt HTTP connection and verify redirect |
| T6 | User DB | E | Default admin account with known password | H | H | Remove default accounts; enforce strong passwords | Attempt login with default credentials |

See [./examples/web-app-threat-model.md](./examples/web-app-threat-model.md) for a full e-commerce threat model.

## Common Pitfalls

Avoid these mistakes when building threat models:

1. **Focusing only on network threats.** Application-layer attacks (injection, broken access control, business logic flaws) are far more common than network-level exploits. Analyze every layer.
2. **Ignoring insider threats.** Disgruntled employees, compromised service accounts, and supply-chain attacks are real. Model what happens when an internal actor turns malicious.
3. **Not prioritizing.** A flat list of 50 threats with no ranking is not actionable. Rate every threat and focus remediation on the top risks first.
4. **Treating the model as a one-time artifact.** Threat models go stale. Revisit them when architecture changes, new integrations are added, or incidents reveal missed threats.
5. **Skipping data flow diagrams.** Without a clear picture of how data moves, you will miss trust boundary crossings where most vulnerabilities live.
6. **Overlooking third-party and supply-chain risks.** External APIs, open-source libraries, and SaaS dependencies introduce threats outside your direct control. Include them in your scope.
7. **Confusing mitigations with aspirations.** "Use encryption" is vague. Specify what is encrypted, with which algorithm, where keys are stored, and how to verify it.

## Data Flow Diagramming Guidance

Effective threat models start with a clear data flow diagram. Follow these practices:

- **Use four element types**: external entities (rectangles), processes (circles), data stores (parallel lines), and data flows (arrows).
- **Mark trust boundaries** with dashed lines. Every crossing is a potential attack surface.
- **Label every flow** with the protocol and data type (e.g., "HTTPS / JSON credentials", "TLS / SQL query").
- **Number each flow** so threats can reference them (e.g., "Flow 3: Browser to API Gateway").
- **Keep it focused.** One diagram per feature or bounded context. A single diagram covering the entire system is too complex to analyze.
- **Iterate.** Start with a Level 0 (context) diagram, then decompose high-risk processes into Level 1 diagrams.

## Output Format

Generate the threat model using this structure:

```markdown
# Threat Model: [System/Feature Name]

## Metadata
- **Date**: [YYYY-MM-DD]
- **Author**: [Name/Team]
- **Version**: [1.0]
- **Status**: [Draft | In Review | Approved]

## System Overview
[Brief description of the system, its purpose, and key components.]

## Assets
- [Asset 1: description and sensitivity level]
- [Asset 2: description and sensitivity level]

## Data Flow Diagram
[Describe or embed the DFD. Number each flow.]

## Trust Boundaries
- [TB1: description — e.g., Internet to DMZ]
- [TB2: description — e.g., Application tier to database tier]

## Assumptions and Scope Exclusions
- [Assumption 1]
- [Out of scope: item]

## Threats

| ID | Flow/Component | STRIDE | Threat | Likelihood | Impact | Risk | Mitigation | Test Action |
|----|---------------|--------|--------|-----------|--------|------|-----------|-------------|
| T1 | [ref] | [S/T/R/I/D/E] | [Description] | [H/M/L] | [H/M/L] | [HxH=Critical] | [Control] | [Verification step] |

## Priority Recommendations
1. [Highest risk: threat ID, recommended action, owner]
2. [Next highest risk]

## Open Questions
- [Unresolved items requiring follow-up]
```

## Related Skills

Use these skills alongside threat modeling for deeper coverage:

- **threat-model-to-test-map** — Convert threats into concrete test cases and traceability matrices.
- **security-misuse-case-generator** — Generate misuse and abuse cases from the attacker perspective.
- **input-validation-test-builder** — Build test scenarios for injection, overflow, and parser abuse threats.
- **config-security-checklist** — Audit deployment configurations against security baselines.
- **secrets-exposure-checker** — Scan for exposed secrets, credentials, and API keys in code and config.

## Reference Files

- [./references/stride.md](./references/stride.md) — Detailed STRIDE methodology guide with per-category questions and real-world examples.
- [./references/common-threats.md](./references/common-threats.md) — Catalog of common threats organized by application layer.
- [./examples/web-app-threat-model.md](./examples/web-app-threat-model.md) — Complete threat model for an e-commerce web application.
