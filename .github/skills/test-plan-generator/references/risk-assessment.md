# Risk Assessment Framework for Test Planning

Use this framework when performing Step 3 (Assess Risk) of the test plan generator procedure. The goal is to allocate testing effort proportionally to risk so that high-risk areas receive deeper, earlier testing while low-risk areas receive appropriate but lighter coverage.

---

## Likelihood × Impact Matrix

Rate each testable area on two dimensions, then combine them into an overall risk rating.

### Likelihood (How likely is a defect in this area?)

| Rating | Definition | Indicators |
|--------|-----------|------------|
| **High** | Defects are expected | New or heavily refactored code, complex business logic, history of bugs in this area, tight deadlines, unfamiliar technology |
| **Medium** | Defects are possible | Moderate changes, some complexity, team has partial familiarity, dependencies on external systems |
| **Low** | Defects are unlikely | Minor changes, well-tested stable code, simple logic, mature and well-understood module |

### Impact (What happens if a defect escapes to production?)

| Rating | Definition | Indicators |
|--------|-----------|------------|
| **High** | Severe business or user harm | Data loss or corruption, security breach, financial miscalculation, complete feature outage, regulatory violation |
| **Medium** | Noticeable degradation | Degraded user experience, workaround available, partial feature failure, performance slowdown |
| **Low** | Minimal user or business effect | Cosmetic issues, minor inconvenience, affects edge-case users only, easy to hotfix |

### Combined Risk Rating

Use this matrix to derive the overall risk rating from likelihood and impact:

|  | **Impact: High** | **Impact: Medium** | **Impact: Low** |
|---|---|---|---|
| **Likelihood: High** | **Critical** | **High** | **Medium** |
| **Likelihood: Medium** | **High** | **Medium** | **Low** |
| **Likelihood: Low** | **Medium** | **Low** | **Low** |

---

## Risk Categories

When identifying risks, consider these categories to ensure comprehensive coverage:

### 1. Functional Risk

Areas where business logic or feature behavior may be incorrect.

- Complex calculations or business rules
- State machines with many transitions
- Conditional logic with multiple branches
- Data transformations and mappings
- Input validation and error handling

**Testing response**: Increase unit and integration test depth. Add boundary value and equivalence partition tests. Prioritize automated regression.

### 2. Integration Risk

Areas where components, services, or systems interact.

- API contracts between services
- Database schema changes and migrations
- Message queue producers and consumers
- Third-party service dependencies
- Authentication and authorization flows across services

**Testing response**: Add contract tests. Test with realistic data volumes. Include failure and timeout scenarios. Verify backward compatibility.

### 3. Data Risk

Areas where data integrity, consistency, or privacy may be compromised.

- Database migrations (schema changes, data backfills)
- Data import/export pipelines
- Caching layers and cache invalidation
- Concurrent write scenarios
- PII handling and data retention policies

**Testing response**: Test migrations on production-like data volumes. Verify rollback procedures. Test concurrent access patterns. Audit data handling against privacy requirements.

### 4. Performance Risk

Areas where response time, throughput, or resource usage may degrade.

- New database queries on large tables
- Endpoints handling high concurrency
- Batch processing jobs
- File upload/download operations
- Real-time features (WebSocket, SSE)

**Testing response**: Run load tests against production-like data. Define and measure SLA targets. Test under sustained load (soak tests). Monitor resource consumption.

### 5. Security Risk

Areas where vulnerabilities may be introduced.

- Authentication and session management changes
- Authorization and access control logic
- User input handling (injection vectors)
- Cryptographic operations
- API endpoint exposure and rate limiting

**Testing response**: Run SAST and DAST scans. Test authentication bypass scenarios. Verify authorization at every access point. Test input validation against OWASP Top 10.

### 6. Infrastructure and Environment Risk

Areas where deployment, configuration, or environment issues may cause failures.

- New infrastructure components (queues, caches, databases)
- Configuration changes (feature flags, environment variables)
- Deployment pipeline changes
- Cross-region or multi-tenant deployments
- Rollback and disaster recovery procedures

**Testing response**: Test deployment and rollback in staging. Verify configuration in each environment. Test feature flag toggling. Run smoke tests after each deployment.

---

## Risk-Based Test Allocation

Use the risk rating to guide how much testing effort to allocate:

| Risk Rating | Test Depth | Automation Priority | Execution Timing | Review Level |
|-------------|-----------|--------------------|--------------------|-------------|
| **Critical** | Exhaustive — cover all paths, boundaries, and error scenarios | Must automate; include in CI | Test first; block release on failures | Peer review of test cases required |
| **High** | Thorough — cover main paths, key boundaries, and likely error scenarios | Automate where feasible; include in regression | Test early in the cycle | Test cases reviewed by lead |
| **Medium** | Standard — cover happy path, primary error paths, and key boundaries | Automate happy path; manual for edge cases | Test in normal sequence | Self-review sufficient |
| **Low** | Light — cover happy path and one error scenario | Automate happy path only or skip automation | Test last or defer if time-constrained | No formal review needed |

---

## Risk Assessment Worksheet

Copy this table into the test plan and fill in one row per testable area:

```markdown
## Risk Assessment

| # | Area | Category | Likelihood | Impact | Rating | Testing Response | Owner |
|---|------|----------|-----------|--------|--------|-----------------|-------|
| 1 | [Area name] | [Functional / Integration / Data / Performance / Security / Infrastructure] | High / Medium / Low | High / Medium / Low | [From matrix] | [What testing to apply] | [Name] |
| 2 | | | | | | | |
| 3 | | | | | | | |
```

### Tips for Filling the Worksheet

- **Be specific about areas**: "Discount calculation with coupon stacking" is better than "pricing."
- **Use evidence for likelihood**: Past defect counts, code complexity metrics, developer familiarity ratings.
- **Use business context for impact**: Revenue impact, user base affected, regulatory exposure, reputational damage.
- **Revisit during testing**: Risk ratings are estimates. Update them as you learn more during test execution.
- **Involve the team**: Risk assessment is more accurate when developers, QA, and product contribute perspectives.

---

## Reassessing Risk During Execution

Risk assessment is not a one-time activity. Reassess when:

- New defects reveal higher-than-expected instability in an area
- Requirements change mid-cycle, altering scope or complexity
- A dependency becomes unavailable or unstable
- Test execution reveals that an area rated Low is actually more complex than expected

Update the risk worksheet and adjust test allocation accordingly. Communicate changes to the team and stakeholders.
