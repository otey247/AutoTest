Below is a practical SDLC testing map you can use as a reference when designing delivery plans, test strategies, or quality gates.

A useful starting point is to separate four things that teams often blur together: **SDLC phase**, **test level**, **test type**, and **test technique**. ISTQB treats these as distinct concepts. Test levels answer *where* a test is applied in the system, test types answer *what quality attribute or objective* is being checked, and static testing covers evaluation of work products without executing code. NIST and OWASP add security-focused practices that should be integrated throughout the SDLC rather than deferred to the end. ([ISTQB][1])

## 1) The big picture

### SDLC phases

A common SDLC view is:

1. Requirements and planning
2. Architecture and design
3. Build and unit development
4. Integration and system assembly
5. System validation
6. Acceptance and release readiness
7. Deployment and operations
8. Maintenance and change

### Test levels

ISTQB’s core test levels are:

* **Component testing**
* **Integration testing**
* **System testing**
* **Acceptance testing** ([ISTQB][1])

### Broad test type families

Across those levels, teams usually run:

* Functional testing
* Non-functional testing
* Structural testing
* Change-related testing
* Security testing
* Static testing and reviews ([ISTQB][1])

## 2) Master phase-to-testing map

| SDLC phase               | Main goal                                        | Test types most common here                                                                                                               | Typical examples                                                                                      |
| ------------------------ | ------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| Requirements / planning  | Validate scope, clarity, feasibility, risk       | Requirements reviews, static testing, acceptance criteria review, threat modeling, test planning                                          | Review user stories, identify missing edge cases, define UAT scenarios, identify security abuse cases |
| Architecture / design    | Validate solution design before build            | Design reviews, architecture reviews, interface reviews, threat modeling, test design, early performance/security planning                | Review API contracts, sequence diagrams, trust boundaries, capacity assumptions                       |
| Build / coding           | Verify code units and developer intent           | Unit testing, component testing, static code analysis, code review, white-box checks, developer security checks                           | Test validation functions, domain rules, error handling, branch logic                                 |
| Integration              | Verify interactions between modules and services | Integration testing, API testing, contract testing, interface testing, data flow testing, smoke testing                                   | Test service A to service B, DB writes, queue consumption, schema compatibility                       |
| System validation        | Verify complete product behavior                 | System testing, end-to-end testing, functional testing, regression, performance, usability, reliability, compatibility, security scanning | Run full purchase flow, batch jobs, error recovery, browser matrix, load test                         |
| Acceptance / pre-release | Confirm fit for business and operations          | UAT, operational acceptance testing, business process validation, compliance checks, release regression                                   | Business users validate order entry, ops validates backup/restore, sign-off checks                    |
| Deployment / operations  | Confirm safe release and production readiness    | Smoke, sanity, installation, configuration, failover, observability validation, post-deploy verification                                  | Verify deployment, health endpoints, dashboards, alerts, rollback paths                               |
| Maintenance / change     | Protect against breakage and drift               | Regression, confirmation testing, patch testing, maintenance testing, production monitoring, incident replay tests                        | Re-run impacted suites after bug fix, replay production defect scenario                               |

This sequencing reflects established testing guidance: static testing starts early, component testing is strongest during build, integration and system testing increase as assembled software emerges, acceptance testing confirms business readiness, and regression/change-related testing continues through maintenance. Security practices such as threat modeling, code scanning, fuzzing, black-box testing, and web app scanning should be integrated across development and testing workflows rather than treated as a final step. ([ISTQB][1])

## 3) Detailed testing map by phase

### A. Requirements and planning

This phase is earlier than many teams realize for testing. ISTQB explicitly includes **static testing** of requirements and other work products, and NIST recommends identifying security requirements and using threat modeling early in development. ([ISTQB][1])

#### 1. Requirements review

**What it is:** Reviewing requirements, stories, use cases, and acceptance criteria without running software.
**Purpose:** Find ambiguity, contradiction, omission, and unverifiable requirements.
**Example:** A story says “system should be fast” but gives no latency target.
**Usually happens in:** Requirements, backlog refinement, sprint planning. ([ISTQB][1])

#### 2. Acceptance criteria validation

**What it is:** Checking whether each requirement has clear pass/fail criteria.
**Purpose:** Ensure later UAT and functional testing can be derived.
**Example:** “User can reset password within 3 steps and receives email within 60 seconds.”
**Usually happens in:** Requirements and design. ([ISTQB][1])

#### 3. Test basis review

**What it is:** Reviewing the artifacts used to design tests, such as stories, process diagrams, APIs, data models, and regulations.
**Purpose:** Prevent bad test design caused by weak inputs.
**Example:** A tester finds that the data dictionary does not define null handling.
**Usually happens in:** Requirements and design. ([ISTQB][1])

#### 4. Risk-based test analysis

**What it is:** Prioritizing tests based on business impact and failure likelihood.
**Purpose:** Focus effort where defects hurt most.
**Example:** Payment authorization gets deeper coverage than profile theme settings.
**Usually happens in:** Planning and is revisited throughout the SDLC. ([ISTQB][1])

#### 5. Threat modeling

**What it is:** Examining design and data flows to identify security risks before implementation.
**Purpose:** Find design-level vulnerabilities early. NIST specifically recommends threat modeling as a developer verification technique, and OWASP includes it in testing guidance.
**Example:** Identify spoofing risk in a public callback endpoint or privilege escalation path in admin workflows.
**Usually happens in:** Requirements and architecture, then updated when design changes. ([NIST Publications][2])

---

### B. Architecture and design

At this phase, teams should test the design itself. This is still mostly static, but it is crucial for catching failures that would be expensive later. NIST’s SSDF stresses integrating security practices into each SDLC implementation. ([NIST Publications][3])

#### 6. Architecture review

**What it is:** Structured review of system architecture against quality goals.
**Purpose:** Validate scalability, resilience, supportability, and security before coding.
**Example:** Detect that a shared database will become a bottleneck or single point of failure.
**Usually happens in:** Design. ([NIST Publications][3])

#### 7. Interface and API design review

**What it is:** Reviewing service contracts, schemas, message formats, and error models.
**Purpose:** Prevent downstream integration defects.
**Example:** Discover versioning conflicts or inconsistent date/time formats across services.
**Usually happens in:** Design, before integration development. ([ISTQB][1])

#### 8. Data design review

**What it is:** Reviewing data models, field constraints, transformations, retention, and lineage.
**Purpose:** Catch defects in validation, reporting, and downstream analytics.
**Example:** Customer ID length mismatch between CRM and billing service.
**Usually happens in:** Design. ([ISTQB][1])

#### 9. Security design review

**What it is:** Reviewing trust boundaries, authentication, authorization, secrets handling, crypto use, and deployment assumptions.
**Purpose:** Prevent security flaws before implementation. OWASP’s guide covers testing of architecture, identity, authentication, authorization, session management, cryptography, business logic, and APIs.
**Example:** Find that an internal admin API is reachable without network segmentation or strong auth.
**Usually happens in:** Design, revisited during build and pre-release. ([OWASP Foundation][4])

#### 10. Test design and traceability setup

**What it is:** Designing test cases and mapping them to requirements and risks.
**Purpose:** Ensure coverage and auditability.
**Example:** Each high-risk requirement gets positive, negative, and edge-case tests.
**Usually happens in:** Design, then refined continuously. ([ISTQB][1])

---

### C. Build and coding

This phase is where dynamic execution starts in earnest. ISTQB identifies **component testing** here, while NIST recommends automated testing, static analysis, structural tests, and checks for hardcoded secrets. ([ISTQB][1])

#### 11. Unit testing

**What it is:** Testing the smallest testable units of logic, usually functions, methods, or classes.
**Purpose:** Verify local correctness quickly and cheaply.
**Example:** Tax calculation for different states and discount combinations.
**Usually happens in:** Build. ([ISTQB][1])

#### 12. Component testing

**What it is:** Testing a module or component with its local collaborators, often slightly broader than a pure unit test.
**Purpose:** Validate a cohesive feature slice before wider integration.
**Example:** Test an order service with mocked gateway and repository layers.
**Usually happens in:** Build. ([ISTQB][1])

#### 13. Code review

**What it is:** Human review of source code.
**Purpose:** Detect logic flaws, maintainability issues, unsafe patterns, and missing tests.
**Example:** Reviewer spots improper null handling or repeated business logic.
**Usually happens in:** Build, before merge. Static testing guidance covers reviews as an early defect detection mechanism. ([ISTQB][1])

#### 14. Static code analysis

**What it is:** Tool-based inspection of code without execution.
**Purpose:** Find code smells, unsafe patterns, potential bugs, style violations, and sometimes security issues.
**Example:** Detect unreachable code, resource leaks, risky deserialization, or unchecked input.
**Usually happens in:** Build and CI. NIST explicitly recommends static code scanning. ([NIST Publications][2])

#### 15. Structural or white-box testing

**What it is:** Tests derived from internal structure such as branches, conditions, paths, or coverage targets.
**Purpose:** Improve logic coverage and reveal hidden execution flaws.
**Example:** Tests force both true and false outcomes of complex validation branches.
**Usually happens in:** Build and component testing. NIST refers to code-based structural test cases. ([NIST Publications][2])

#### 16. Developer security checks

**What it is:** Local security-oriented checks such as dependency checks, secret scanning, unsafe config detection, and input validation testing at code level.
**Purpose:** Stop security regressions before integration.
**Example:** Detect a hardcoded token in a repository or missing output encoding.
**Usually happens in:** Build and CI. NIST recommends heuristic tools for secrets and built-in checks/protections. ([NIST Publications][2])

---

### D. Integration and assembly

This is where independently correct components often fail together. ISTQB separates **integration testing** from component and system levels. ([ISTQB][1])

#### 17. Integration testing

**What it is:** Testing interactions between components, subsystems, or external interfaces.
**Purpose:** Find defects in communication, sequencing, contracts, and data flow.
**Example:** Order API writes to DB, emits queue event, and inventory service consumes it.
**Usually happens in:** Integration phase, and often continues through system testing. ([ISTQB][1])

#### 18. API testing

**What it is:** Testing service endpoints and contract behavior directly.
**Purpose:** Verify request/response rules, auth, error handling, idempotency, and data integrity.
**Example:** `POST /orders` returns 201 on success, 400 on malformed payload, 409 on duplicate.
**Usually happens in:** Integration and system phases. OWASP includes API testing in its web security testing material. ([OWASP Foundation][5])

#### 19. Contract testing

**What it is:** Verifying that producer and consumer expectations match on an interface.
**Purpose:** Prevent breaking changes across teams and services.
**Example:** Consumer expects `customerId` as string but provider changed it to integer.
**Usually happens in:** Build, CI, and integration. This is a practical form of interface verification even when not named separately in core syllabi. Supported by the broader concept of interface and integration testing. ([ISTQB][1])

#### 20. Data flow and ETL testing

**What it is:** Verifying movement, transformation, and persistence of data across system boundaries.
**Purpose:** Catch truncation, mapping errors, duplicates, ordering issues, and lost messages.
**Example:** Address normalization corrupts apartment numbers during sync.
**Usually happens in:** Integration and system validation. ([ISTQB][1])

#### 21. Smoke testing

**What it is:** A shallow, broad check that critical flows work after a build or deployment.
**Purpose:** Determine whether deeper testing is worth running.
**Example:** App starts, login works, main dashboard loads, primary API responds.
**Usually happens in:** Integration, pre-system test, deployment. ([ISTQB][1])

#### 22. Sanity testing

**What it is:** Narrow validation of a focused area after a small change or fix.
**Purpose:** Confirm the change appears stable before broader regression.
**Example:** After fixing invoice PDF generation, only invoice creation and rendering paths are checked first.
**Usually happens in:** Integration, system, or hotfix validation. This is commonly treated as a subset of regression/change-related testing. ([ISTQB][1])

---

### E. System validation

At this point the whole assembled product is being evaluated against functional and quality expectations. ISTQB treats **system testing** as a distinct level, and its syllabus differentiates functional and non-functional test types. ([ISTQB][1])

#### 23. Functional testing

**What it is:** Testing whether the system does what it is supposed to do.
**Purpose:** Verify business behavior against requirements.
**Example:** User can create an order, apply discount, submit payment, and receive confirmation.
**Usually happens in:** Component, integration, system, and acceptance, but most visible at system level. ([ISTQB][1])

#### 24. End-to-end testing

**What it is:** Testing a complete business workflow across multiple systems.
**Purpose:** Verify real-world business flow, not just isolated features.
**Example:** Customer signup to payment to fulfillment to email notification.
**Usually happens in:** System and pre-release validation. ([ISTQB][1])

#### 25. Regression testing

**What it is:** Re-running tests to detect unintended side effects from changes.
**Purpose:** Protect existing behavior.
**Example:** A shipping change accidentally breaks tax calculation.
**Usually happens in:** Integration, system, acceptance, release, and maintenance. ISTQB treats regression as a key change-related activity. ([ISTQB][1])

#### 26. Confirmation testing

**What it is:** Re-testing a previously failed scenario after a fix.
**Purpose:** Confirm the original defect was actually resolved.
**Example:** Re-run the exact steps that previously crashed checkout.
**Usually happens in:** Any phase after a defect fix; commonly system and maintenance. ([ISTQB][1])

#### 27. Exploratory testing

**What it is:** Simultaneous learning, test design, and test execution.
**Purpose:** Reveal defects that scripted cases miss, especially in complex or changing domains.
**Example:** Tester intentionally varies field order, timing, browser tabs, and invalid state transitions.
**Usually happens in:** System, acceptance, and maintenance; can also happen early on prototypes. ISTQB recognizes exploratory testing as a useful dynamic approach. ([ISTQB][1])

#### 28. Usability testing

**What it is:** Evaluating ease of use, clarity, efficiency, and user error resistance.
**Purpose:** Ensure the system is understandable and practical.
**Example:** Users cannot find the save action because it is buried in an overflow menu.
**Usually happens in:** Design validation, system test, and acceptance. Non-functional testing categories in ISTQB include usability-related concerns. ([ISTQB][1])

#### 29. Accessibility testing

**What it is:** Evaluating whether users with disabilities can effectively use the system.
**Purpose:** Reduce barriers and meet accessibility obligations.
**Example:** Screen reader order is wrong, keyboard focus is trapped in a modal.
**Usually happens in:** Design review, build, system test, and pre-release. This fits under non-functional quality validation and is often paired with usability validation. ([ISTQB][1])

#### 30. Compatibility testing

**What it is:** Testing behavior across browsers, devices, operating systems, databases, or environments.
**Purpose:** Ensure consistent operation in supported platforms.
**Example:** File upload works in Chrome but fails in Safari mobile.
**Usually happens in:** System validation and release readiness. Non-functional test categories include compatibility/portability concerns. ([ISTQB][1])

#### 31. Installation and upgrade testing

**What it is:** Testing deployment, installation, migration, rollback, and upgrade paths.
**Purpose:** Ensure the product can be safely delivered and updated.
**Example:** Upgrading from v4 to v5 preserves settings and data.
**Usually happens in:** System, pre-release, and operations. ([ISTQB][1])

#### 32. Reliability and recovery testing

**What it is:** Testing resilience, restart behavior, data recovery, and fault tolerance.
**Purpose:** Validate service continuity and failure handling.
**Example:** Service restarts after node loss without corrupting in-flight orders.
**Usually happens in:** System, pre-release, and operations. These are standard non-functional concerns aligned with secure and operational SDLC guidance. ([ISTQB][1])

#### 33. Performance testing

**What it is:** Measuring speed, throughput, scalability, and stability under workload.
**Purpose:** Verify response times and capacity expectations.
**Example:** Search API must respond in under 300 ms at 500 concurrent users.
**Usually happens in:** Design planning, system test, pre-release, and periodically in production-like environments. ([ISTQB][1])

Subtypes commonly include:

* **Load testing:** expected business load
* **Stress testing:** beyond expected limits
* **Spike testing:** sudden bursts
* **Endurance/soak testing:** sustained load over time
* **Scalability testing:** effect of adding resources
  These are all practical subtypes of performance-oriented non-functional testing. ([ISTQB][1])

---

### F. Security testing

Security testing should not be a final gate only. NIST’s SSDF says secure development practices need to be integrated into each SDLC implementation, and NIST IR 8397 recommends threat modeling, automated testing, static scanning, structural tests, fuzzing, black-box tests, and web app scanners. OWASP’s testing guide provides specific areas like authentication, authorization, session management, input validation, business logic, client-side testing, and API testing. ([NIST Publications][2])

#### 34. Static application security testing (SAST)

**What it is:** Analyzing source or binaries for security weaknesses without execution.
**Purpose:** Find insecure coding patterns early.
**Example:** SQL injection sink, insecure crypto use, unsafe deserialization.
**Usually happens in:** Build and CI. ([NIST Publications][2])

#### 35. Dynamic application security testing (DAST)

**What it is:** Testing a running application from the outside.
**Purpose:** Find exploitable runtime weaknesses.
**Example:** Missing security headers, auth bypass, reflected XSS.
**Usually happens in:** Integration, system, pre-release. This aligns with NIST’s black-box testing and web app scanners, plus OWASP web application testing. ([NIST Publications][2])

#### 36. Interactive application security testing (IAST)

**What it is:** Runtime-assisted testing that combines execution context with vulnerability detection.
**Purpose:** Improve precision compared with purely static or purely black-box methods.
**Example:** Tool observes tainted data flow from request to vulnerable sink during a test run.
**Usually happens in:** Integration and system environments. This is an industry practice consistent with the balanced testing approach OWASP advocates, though not the only accepted model. ([OWASP Foundation][4])

#### 37. Software composition analysis (SCA)

**What it is:** Checking third-party libraries and dependencies for known issues and policy problems.
**Purpose:** Reduce supply-chain risk.
**Example:** A transitive library contains a critical CVE.
**Usually happens in:** Build, CI, release, and maintenance. This fits NIST’s secure software verification and SSDF practices. ([NIST Publications][3])

#### 38. Authentication testing

**What it is:** Testing login, MFA, password policies, reset flows, and session establishment.
**Purpose:** Prevent identity compromise.
**Example:** Password reset token can be replayed or login lacks brute-force protection.
**Usually happens in:** Integration, system, security test, pre-release. OWASP explicitly covers authentication testing. ([OWASP Foundation][5])

#### 39. Authorization testing

**What it is:** Testing access control and privilege boundaries.
**Purpose:** Prevent horizontal and vertical privilege escalation.
**Example:** Normal user can access admin invoice export endpoint by changing URL.
**Usually happens in:** Integration, system, acceptance for regulated workflows. OWASP explicitly covers authorization testing. ([OWASP Foundation][5])

#### 40. Session management testing

**What it is:** Testing session IDs, expiration, fixation, logout, and secure transport.
**Purpose:** Prevent session hijacking and persistence flaws.
**Example:** Logout leaves valid API token active.
**Usually happens in:** System and security testing. ([OWASP Foundation][5])

#### 41. Input validation testing

**What it is:** Testing how the application handles malformed, malicious, and boundary input.
**Purpose:** Prevent injection, overflow, parser abuse, and logic bypass.
**Example:** HTML in comments field triggers stored XSS.
**Usually happens in:** Unit, integration, system, and security testing. OWASP explicitly includes input validation testing. ([OWASP Foundation][5])

#### 42. Business logic testing

**What it is:** Testing abuse of legitimate workflows rather than purely technical flaws.
**Purpose:** Catch issues like bypassing approvals, price manipulation, sequence abuse, or quota evasion.
**Example:** User applies discount twice by exploiting step order.
**Usually happens in:** System, acceptance, and security testing. OWASP explicitly includes business logic testing. ([OWASP Foundation][5])

#### 43. Cryptography testing

**What it is:** Testing key management, algorithm usage, storage, rotation, and transport protection.
**Purpose:** Ensure sensitive data is actually protected.
**Example:** Encryption exists but keys are stored in plaintext config.
**Usually happens in:** Design review, build, system, and security testing. OWASP includes weak cryptography testing. ([OWASP Foundation][5])

#### 44. Configuration and deployment security testing

**What it is:** Testing infrastructure and runtime configuration for insecure defaults or exposure.
**Purpose:** Prevent environment-level vulnerabilities.
**Example:** Debug mode enabled in production or storage bucket publicly readable.
**Usually happens in:** Integration, pre-release, deployment, and operations. OWASP covers configuration and deployment management testing. ([OWASP Foundation][6])

#### 45. Fuzz testing

**What it is:** Feeding unexpected, random, malformed, or semi-random input to a system.
**Purpose:** Reveal crashes, parser bugs, memory issues, and unhandled states.
**Example:** API crashes when deeply nested JSON is submitted.
**Usually happens in:** Build for libraries, integration/system for services, and security test cycles. NIST explicitly recommends fuzzing. ([NIST Publications][2])

#### 46. Penetration testing

**What it is:** Simulated adversarial testing against a deployed or deployable system.
**Purpose:** Determine exploitability and realistic attack paths.
**Example:** Tester chains weak authorization with ID enumeration to exfiltrate records.
**Usually happens in:** Late system test, pre-release, periodically in production-like environments. OWASP explicitly includes penetration testing in its guidance. ([OWASP Foundation][4])

---

### G. Acceptance, release, and operations

Acceptance is its own test level in ISTQB, not just “system test done by business.” It validates fitness for use from business or operational perspectives. ([ISTQB][1])

#### 47. User acceptance testing (UAT)

**What it is:** Business stakeholders validate that the solution supports intended real-world use.
**Purpose:** Confirm business fitness, not just technical correctness.
**Example:** Finance team validates month-end reconciliation workflow.
**Usually happens in:** Acceptance and pre-release. ([ISTQB][1])

#### 48. Operational acceptance testing (OAT)

**What it is:** Validating the system can be run and supported in production.
**Purpose:** Ensure backup, restore, monitoring, alerting, failover, job scheduling, access administration, and support processes work.
**Example:** Restore from backup succeeds within RTO/RPO targets and alerts route correctly.
**Usually happens in:** Pre-release and deployment readiness. This aligns with SDLC security control verification and operational readiness expectations. ([NIST Publications][7])

#### 49. Installation / deployment verification testing

**What it is:** Testing the deployment itself and confirming the app is healthy post-release.
**Purpose:** Catch config drift, missing secrets, schema issues, or startup failure.
**Example:** Application deploys successfully but background workers fail to connect to queue.
**Usually happens in:** Deployment and release. ([NIST Publications][7])

#### 50. Monitoring and observability validation

**What it is:** Testing telemetry, logs, traces, alerts, dashboards, and runbooks.
**Purpose:** Ensure teams can detect and diagnose failures.
**Example:** Checkout latency alert never fires because metrics are not tagged properly.
**Usually happens in:** Pre-release, deployment, and operations. NIST’s SDLC security guidance emphasizes test and evaluation evidence for operational decisions. ([NIST Publications][7])

#### 51. Maintenance testing

**What it is:** Testing after fixes, enhancements, infrastructure changes, dependency updates, or environment moves.
**Purpose:** Ensure the changed system still works and past defects do not return.
**Example:** OS patch changes file permissions and breaks report exports.
**Usually happens in:** Maintenance and change windows. ISTQB explicitly addresses maintenance testing and change-related testing. ([ISTQB][1])

---

## 4) A cleaner way to think about “what happens when”

### Usually earliest

* Requirements reviews
* Acceptance criteria review
* Risk analysis
* Threat modeling
* Architecture and design review
* Test planning and traceability setup ([ISTQB][1])

### Usually strongest during build

* Unit tests
* Component tests
* Code review
* Static analysis
* Structural coverage tests
* Developer security checks ([ISTQB][1])

### Usually strongest during integration

* Integration tests
* API tests
* Contract/interface tests
* Smoke tests
* Data flow tests ([ISTQB][1])

### Usually strongest during full-system validation

* Functional tests
* End-to-end tests
* Regression tests
* Performance tests
* Reliability/recovery tests
* Compatibility tests
* Security dynamic testing ([ISTQB][1])

### Usually strongest near release

* UAT
* OAT
* Installation and upgrade tests
* Penetration tests
* Release smoke tests
* Rollback and observability checks ([OWASP Foundation][4])

### Continuous across the lifecycle

* Regression
* Confirmation testing after fixes
* Static reviews as artifacts change
* Risk reassessment
* Security verification
* Monitoring and production validation ([ISTQB][1])

---

## 5) What teams most often get wrong

A common failure is treating testing as only a late execution phase. Official testing and secure SDLC guidance point the other direction: start with static testing and risk analysis, verify continuously, and integrate security throughout the lifecycle. Another common mistake is confusing **test levels** with **test types**. For example, “system testing” is a level, while “performance testing” and “security testing” are types that can occur at multiple levels, though they are most visible at system and pre-release stages. ([ISTQB][1])

---

## 6) A practical default test stack for most enterprise apps

For a typical business web application, a sensible baseline usually looks like this:

* **Requirements/design:** reviews, acceptance criteria review, threat modeling
* **Build:** unit tests, code review, static analysis, secret scanning
* **Integration:** API tests, contract tests, service integration tests, smoke
* **System:** functional, regression, end-to-end, compatibility, performance
* **Security:** authz/authn, input validation, configuration checks, DAST, fuzzing where relevant
* **Release:** UAT, OAT, deployment verification, rollback validation
* **Maintenance:** regression, confirmation, monitoring-driven test additions ([ISTQB][1])

---

## 7) One-line cheat sheet

* **Static testing** asks: “Is the artifact itself good enough before execution?”
* **Component testing** asks: “Does this piece of code work on its own?”
* **Integration testing** asks: “Do these pieces work together?”
* **System testing** asks: “Does the whole product work?”
* **Acceptance testing** asks: “Is it fit for business and operations?”
* **Regression testing** asks: “Did this change break something else?”
* **Security testing** asks: “Can it be abused, bypassed, or exploited?”
* **Performance testing** asks: “Will it hold up under real load?” ([ISTQB][1])

If you want, I can turn this next into a **downloadable RACI-style SDLC testing matrix** with columns for **phase, test type, owner, entry criteria, exit criteria, tools, and sample deliverables**.

[1]: https://istqb.org/?download_id=3345&sdm_process_download=1&utm_source=chatgpt.com "Certified Tester Foundation Level Syllabus"
[2]: https://nvlpubs.nist.gov/nistpubs/ir/2021/NIST.IR.8397.pdf?utm_source=chatgpt.com "Guidelines on Minimum Standards for Developer Verification ..."
[3]: https://nvlpubs.nist.gov/nistpubs/specialpublications/nist.sp.800-218.pdf?utm_source=chatgpt.com "Secure Software Development Framework (SSDF) Version 1.1"
[4]: https://owasp.org/www-project-web-security-testing-guide/latest/?utm_source=chatgpt.com "WSTG - Latest"
[5]: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/?utm_source=chatgpt.com "Web Application Security Testing"
[6]: https://owasp.org/www-project-web-security-testing-guide/v42/?utm_source=chatgpt.com "WSTG - v4.2"
[7]: https://nvlpubs.nist.gov/nistpubs/legacy/sp/nistspecialpublication800-64r2.pdf?utm_source=chatgpt.com "NIST Special Publication 800-64 Revision 2, Security ..."
