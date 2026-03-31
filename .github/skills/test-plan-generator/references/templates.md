# Test Plan Templates

Ready-made templates for three plan scopes. Copy the appropriate template and fill in the bracketed placeholders.

---

## Feature-Level Template

Use for a single feature, user story, or bounded change. Keep it to 1–2 pages.

```markdown
# Test Plan: [Feature Name]

**Author**: [Name]
**Date**: [YYYY-MM-DD]
**Status**: Draft | In Review | Approved

## 1. Scope

### In Scope
- [Feature area 1: brief description of what will be tested]
- [Feature area 2: brief description]

### Out of Scope
- [Area excluded]: [Reason — e.g., unchanged, owned by another team, deferred]

### Assumptions
- [Assumption 1 — e.g., dependency X is deployed and stable]
- [Assumption 2]

## 2. Risk Assessment

| Area | Likelihood | Impact | Risk Rating | Notes |
|------|-----------|--------|-------------|-------|
| [Area 1] | High / Medium / Low | High / Medium / Low | Critical / High / Medium / Low | [Why this rating] |
| [Area 2] | | | | |

## 3. Test Approach

| Test Level | Test Types | Method | Tools | Owner |
|------------|-----------|--------|-------|-------|
| Unit | [Functional, boundary] | Automated | [Jest / pytest] | Dev |
| Integration | [API contract, data flow] | Automated | [Supertest / Pact] | Dev / QA |
| E2E | [User journey, error flows] | Automated + exploratory | [Playwright / Cypress] | QA |

### Test Data Strategy
- [How test data is created, seeded, or managed — e.g., factory fixtures, anonymized snapshots]

### Environment
- [Which environment(s) — e.g., local for unit, CI for integration, staging for E2E]

## 4. Entry Criteria
- [ ] Code merged and CI build green
- [ ] Test environment provisioned and accessible
- [ ] Test data seeded
- [ ] Acceptance criteria reviewed with product

## 5. Exit Criteria
- [ ] All planned test cases executed
- [ ] No open Critical or High defects
- [ ] [Coverage threshold met — e.g., 80% branch coverage for new code]
- [ ] Exploratory testing session completed for high-risk areas

## 6. Risks and Mitigations

| Risk | Impact | Likelihood | Mitigation |
|------|--------|-----------|------------|
| [Risk 1] | [What happens if it occurs] | High / Medium / Low | [Concrete action to reduce or avoid] |

## 7. Schedule

| Milestone | Target Date | Owner |
|-----------|------------|-------|
| Unit tests complete | [Date] | [Name] |
| Integration tests complete | [Date] | [Name] |
| E2E + exploratory complete | [Date] | [Name] |
| Test summary report | [Date] | [Name] |

## 8. Deliverables
- [ ] This test plan (reviewed and approved)
- [ ] Test cases / automation scripts
- [ ] Defect reports (if any)
- [ ] Test summary report
```

---

## Sprint-Level Template

Use when planning testing for all stories in a sprint. Aggregates scope and provides a shared view of risks and environment needs.

```markdown
# Sprint Test Plan: [Sprint Name / Number]

**Sprint dates**: [Start] – [End]
**Author**: [Name]
**Date**: [YYYY-MM-DD]

## 1. Sprint Scope Overview

| Story / Task | Risk Rating | Test Levels | Owner |
|-------------|-------------|-------------|-------|
| [Story 1: Title] | High | Unit, Integration, E2E | [Name] |
| [Story 2: Title] | Medium | Unit, Integration | [Name] |
| [Story 3: Title] | Low | Unit | [Name] |

### Out of Scope
- [Items explicitly excluded from this sprint's testing]

### Cross-Story Dependencies
- [Story A depends on Story B's API being complete before integration testing]
- [Shared test data needed for Stories C and D]

## 2. Aggregated Risk View

| Risk Area | Affected Stories | Rating | Mitigation |
|-----------|-----------------|--------|------------|
| [Database migration] | Story 1, Story 3 | High | [Run migration in test env before sprint day 3] |
| [Third-party API rate limits] | Story 2 | Medium | [Use stub in CI, real API in nightly] |

## 3. Test Approach by Level

| Test Level | Stories Covered | Method | Tools | Environment |
|------------|----------------|--------|-------|-------------|
| Unit | All | Automated (CI) | [Jest] | Local + CI |
| Integration | Stories 1, 2 | Automated (CI) | [Supertest] | CI |
| E2E | Story 1 | Automated + exploratory | [Playwright] | Staging |
| Regression | All (impacted areas) | Automated | [Existing suite] | CI |

### Environment Needs
- [ ] Staging environment reserved for [dates]
- [ ] Test database seeded with [data set]
- [ ] Feature flags configured: [list]

## 4. Entry Criteria
- [ ] Sprint stories are groomed with testable acceptance criteria
- [ ] CI pipeline green on sprint branch
- [ ] Environment provisioned and accessible

## 5. Exit Criteria
- [ ] All story-level test plans executed
- [ ] Regression suite passes with no new failures
- [ ] No open Critical defects; High defects triaged with product owner
- [ ] Sprint test summary reviewed in retrospective

## 6. Schedule

| Milestone | Target | Notes |
|-----------|--------|-------|
| Story-level unit tests | Sprint day [N] | Each dev owns their story |
| Integration tests | Sprint day [N] | After stories are merged |
| E2E + regression run | Sprint day [N] | On staging |
| Exploratory testing | Sprint day [N] | Focus on high-risk stories |
| Test summary report | Sprint day [N] | Input to sprint review |

## 7. Deliverables
- [ ] This sprint test plan
- [ ] Per-story test cases (linked from story tickets)
- [ ] Regression run results
- [ ] Defect reports
- [ ] Sprint test summary
```

---

## Release-Level Template

Use for a release candidate covering multiple sprints or a major version. This is the most formal and comprehensive template.

```markdown
# Release Test Plan: [Release Version / Name]

**Release date**: [YYYY-MM-DD]
**Author**: [Name]
**Approvers**: [Names / Roles]
**Status**: Draft | In Review | Approved

## 1. Release Overview
[Brief description of what this release includes — major features, epics, infrastructure changes.]

### Release Goals
- [Goal 1]
- [Goal 2]

## 2. Scope

### In Scope
| Feature / Epic | Description | Risk Rating | Test Lead |
|---------------|-------------|-------------|-----------|
| [Feature 1] | [Brief description] | High | [Name] |
| [Feature 2] | [Brief description] | Medium | [Name] |
| [Infrastructure change] | [Brief description] | High | [Name] |

### Out of Scope
- [Feature / area excluded and reason]

### Assumptions and Dependencies
- [External dependency 1 — e.g., partner API v2 available by date X]
- [Internal dependency — e.g., database migration completed before release testing]

## 3. Risk Assessment

| Risk Area | Likelihood | Impact | Rating | Mitigation | Owner |
|-----------|-----------|--------|--------|------------|-------|
| [Complex migration logic] | High | High | Critical | [Dedicated migration test suite + rollback drill] | [Name] |
| [New payment provider integration] | Medium | High | High | [Contract tests + manual verification in sandbox] | [Name] |
| [Performance under Black Friday load] | Medium | High | High | [Load test at 2x projected traffic] | [Name] |

See the risk assessment framework in ./risk-assessment.md for rating guidance.

## 4. Test Strategy

### Test Levels and Types

| Test Level | Scope | Method | Tools | Environment | Owner |
|------------|-------|--------|-------|-------------|-------|
| Unit | All new/changed code | Automated (CI) | [Jest / pytest] | CI | Dev teams |
| Component | Service-level integration | Automated (CI) | [Testcontainers] | CI | Dev teams |
| Integration | Cross-service APIs | Automated (CI + nightly) | [Pact / Supertest] | Staging | SDET |
| E2E | Critical user journeys | Automated + manual | [Playwright] | Pre-prod | QA |
| Regression | Full regression suite | Automated | [Existing suite] | Staging | QA |
| Performance | Load, stress, soak | Automated | [k6 / JMeter] | Perf env | SDET |
| Security | OWASP Top 10, auth flows | Automated + manual | [ZAP / Burp] | Staging | Security |
| Accessibility | WCAG 2.1 AA | Automated + manual | [axe / manual audit] | Staging | QA |

### Regression Strategy
- Full regression suite run on release candidate
- Focused regression on areas affected by this release
- Smoke suite run after each deployment to staging and pre-prod

### Performance Testing
- Load profile: [X concurrent users, Y requests/sec]
- Acceptance criteria: [p95 latency < 500ms, error rate < 0.1%]
- Soak test: [Duration and success criteria]

### Security Testing
- SAST scan on all new code (CI)
- DAST scan on staging after E2E deployment
- Manual penetration testing for [high-risk areas]

## 5. Environment Plan

| Environment | Purpose | Availability | Owner |
|-------------|---------|-------------|-------|
| CI | Unit + component + integration | Always | DevOps |
| Staging | E2E, regression, security, accessibility | [Dates] | DevOps |
| Perf | Performance testing | [Dates] | DevOps |
| Pre-prod | Final validation, smoke | [Dates] | Release Mgr |

### Test Data
- [Strategy for each environment — synthetic, anonymized, subset of production]
- [Data refresh schedule]

## 6. Entry Criteria
- [ ] All features code-complete and merged to release branch
- [ ] CI pipeline green (unit + component + integration)
- [ ] Staging environment provisioned with release candidate build
- [ ] Test data seeded in all environments
- [ ] Feature-level test plans reviewed and approved
- [ ] Performance test environment baselined

## 7. Exit Criteria (Go / No-Go)
- [ ] All Critical and High test cases executed and passing
- [ ] Zero open Critical defects; all High defects resolved or waived by product owner
- [ ] Regression suite passes with no new failures
- [ ] Performance targets met (p95 latency, error rate, throughput)
- [ ] Security scan shows no Critical or High vulnerabilities
- [ ] Accessibility audit passes WCAG 2.1 AA
- [ ] Rollback procedure tested and documented
- [ ] Stakeholder sign-off obtained

## 8. Schedule

| Phase | Start | End | Activities |
|-------|-------|-----|-----------|
| Test planning | [Date] | [Date] | Finalize this plan, prepare test data and environments |
| Feature testing | [Date] | [Date] | Execute feature-level test plans |
| Integration + E2E | [Date] | [Date] | Cross-service integration, user journey tests |
| Regression | [Date] | [Date] | Full regression suite on release candidate |
| Performance + Security | [Date] | [Date] | Load tests, security scans |
| UAT | [Date] | [Date] | User acceptance testing with stakeholders |
| Go / No-Go decision | [Date] | — | Review exit criteria, make release decision |

## 9. Roles and Responsibilities

| Role | Name | Responsibilities |
|------|------|-----------------|
| Test Lead | [Name] | Own this plan, coordinate execution, report status |
| Dev Lead | [Name] | Unit/component tests, defect fixes, code reviews |
| SDET | [Name] | Automation, performance tests, CI pipeline |
| QA | [Name] | E2E, regression, exploratory, accessibility |
| Product Owner | [Name] | UAT, defect triage, sign-off |
| Release Manager | [Name] | Environment coordination, go/no-go decision |

## 10. Reporting and Communication
- **Daily**: Test execution status in standup (pass/fail/blocked counts)
- **Weekly**: Test summary email to stakeholders (coverage, defect trends, risks)
- **Milestone**: Phase completion report (feature testing done, regression done, etc.)
- **Release**: Final test summary report with go/no-go recommendation

## 11. Deliverables
- [ ] This release test plan (approved)
- [ ] Feature-level test plans (per feature)
- [ ] Test cases and automation scripts
- [ ] Regression suite run results
- [ ] Performance test report
- [ ] Security scan report
- [ ] Accessibility audit report
- [ ] Defect reports and triage log
- [ ] Final test summary report with go/no-go recommendation
```
