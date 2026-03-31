---
name: performance-test-plan-generator
description: 'Generate performance test plans that define objectives, load profiles, acceptance criteria, and tooling. Use when planning performance testing for a release, defining SLA validation tests, creating load test strategies, or setting up performance baselines. Covers load, stress, spike, soak, and scalability testing with tool selection guidance for k6, JMeter, Gatling, Locust, and Artillery.'
---

# Performance Test Plan Generator

Generate comprehensive performance test plans with objectives, load profiles, acceptance criteria, environment requirements, and tooling recommendations.

## When to Use

- Planning performance testing for a release, migration, or new feature
- Defining SLA validation tests against contractual or internal targets
- Creating a load testing strategy for a system that has never been load-tested
- Setting up performance baselines before architectural changes
- Preparing for expected traffic increases (launches, marketing campaigns, seasonal peaks)
- Evaluating whether infrastructure scaling plans are adequate

## Procedure

Follow these steps in order. Each step builds on the previous one.

### Step 1 — Gather Performance Requirements

Ask the user (or extract from provided context) the following:

1. **SLAs and SLOs**: Contractual or internal service-level targets.
2. **Response time targets**: P50, P95, P99 latency for key transactions.
3. **Throughput targets**: Requests per second, transactions per minute, or messages per second.
4. **Concurrency requirements**: Peak simultaneous users or connections.
5. **Resource limits**: CPU, memory, network bandwidth, disk I/O ceilings.
6. **Business context**: Upcoming launches, seasonal patterns, growth projections.
7. **Current production metrics**: If available, use real data as the baseline. Request access-log summaries, APM dashboards, or monitoring exports.

If the user cannot provide specific numbers, use industry benchmarks or derive them from production analytics. Always state assumptions explicitly.

### Step 2 — Identify Critical Transactions

List the transactions that matter most, ordered by business impact:

- Revenue-generating paths (checkout, payment, subscription)
- High-frequency paths (search, feed loading, API polling)
- Authentication and session establishment
- Background jobs that compete for resources (batch imports, report generation)
- Third-party integrations that may become bottlenecks

Assign each transaction a **weight** reflecting its share of total traffic.

### Step 3 — Define Test Types

Select the test types that apply. Not every project needs all five.

| Test Type | Purpose | When to Include |
|-----------|---------|-----------------|
| **Load test** | Validate behavior under expected production load | Always |
| **Stress test** | Find the breaking point beyond expected load | When max capacity is unknown |
| **Spike test** | Validate response to sudden traffic bursts | When flash-traffic events are possible |
| **Soak test** | Detect memory leaks, connection pool exhaustion, log growth | Before major releases or for long-running services |
| **Scalability test** | Measure impact of adding or removing resources | When auto-scaling or capacity planning is in scope |

### Step 4 — Design Load Profiles

For each selected test type, define:

- **Virtual users / request rate**: Starting, peak, and steady-state values.
- **Ramp-up period**: Gradual increase to avoid thundering-herd artifacts.
- **Steady-state duration**: Long enough to collect statistically significant samples (minimum 5 minutes for load tests, 2+ hours for soak tests).
- **Ramp-down period**: Graceful decrease to observe recovery behavior.
- **Think time**: Realistic pauses between user actions. Omitting think time creates unrealistic load.
- **Pacing**: Fixed interval vs. dynamic pacing to maintain target throughput.

Use production traffic patterns when available. See the **load-profile-builder** skill for detailed profile design.

### Step 5 — Define Acceptance Criteria

Write measurable, binary pass/fail criteria. Examples:

| Metric | Target | Critical Threshold | Measurement Window |
|--------|--------|--------------------|-------------------|
| Response time P95 | Under 300 ms | Under 800 ms | Steady-state period |
| Response time P99 | Under 1 s | Under 3 s | Steady-state period |
| Throughput | 500 req/s sustained | 400 req/s minimum | Steady-state period |
| Error rate | Under 0.1 % | Under 1 % | Entire test run |
| CPU utilization | Under 70 % | Under 90 % | Steady-state period |
| Memory utilization | Under 75 % | Under 90 % | Steady-state period |

Exclude the ramp-up and ramp-down periods from acceptance-criteria evaluation unless specifically testing ramp behavior.

### Step 6 — Specify Environment and Data

Define the test environment requirements:

- **Environment parity**: State how closely the test environment mirrors production (instance sizes, database size, network topology). Document known differences.
- **Test data**: Volume, variety, and how it is seeded. Specify whether production-like data is required.
- **External dependencies**: Identify third-party services. Decide whether to use live endpoints, sandboxes, or mocks/stubs. Document rate limits.
- **Isolation**: Confirm that performance tests will not affect production or other test activities.

### Step 7 — Select Tooling

Recommend tools based on the technology stack, team expertise, and test requirements. Use the comparison below as a starting point; see `./references/tools.md` for a detailed breakdown.

| Tool | Best For | Protocol Support | Scripting |
|------|----------|-----------------|-----------|
| **k6** | Developer-centric teams, CI/CD integration | HTTP, WebSocket, gRPC | JavaScript/TypeScript |
| **JMeter** | GUI-based test design, broad protocol coverage | HTTP, JDBC, JMS, LDAP, FTP | XML (GUI), Groovy |
| **Gatling** | High-throughput HTTP testing, Scala/Java shops | HTTP, WebSocket, JMS | Scala, Java, Kotlin |
| **Locust** | Python teams, custom load shapes | HTTP (extensible) | Python |
| **Artillery** | Serverless and microservice testing | HTTP, WebSocket, Socket.io, gRPC | YAML + JavaScript |

### Step 8 — Define Execution Plan

Specify the run schedule:

- **Pre-test checklist**: Monitoring enabled, alerts silenced for test env, test data seeded, warm-up completed.
- **Run order**: Execute baseline load test first, then stress, spike, and soak.
- **Iteration strategy**: Plan at least two runs per test type to confirm reproducibility.
- **Results collection**: Define which dashboards, logs, and artifacts to capture.
- **Reporting**: Who reviews results, what format, and decision criteria for pass/fail.

### Step 9 — Assemble the Plan Document

Produce the plan using the output format below. Fill every section with concrete values — avoid placeholders where possible.

## Output Format

```markdown
# Performance Test Plan: [System/Feature Name]

## 1. Overview
- **System under test**: [Name, version, deployment topology]
- **Test objective**: [Concise statement of what this plan validates]
- **Business context**: [Why now — launch, migration, SLA review, etc.]

## 2. Acceptance Criteria
| Metric | Target | Critical Threshold | Window |
|--------|--------|--------------------|--------|
| P95 response time | < 300 ms | < 800 ms | Steady state |
| Throughput | 500 req/s | 400 req/s min | Steady state |
| Error rate | < 0.1 % | < 1 % | Full run |
| CPU utilization | < 70 % | < 90 % | Steady state |

## 3. Critical Transactions
| # | Transaction | Method | Weight | Target P95 |
|---|------------|--------|--------|-----------|
| 1 | [Name] | [GET/POST/...] | [X %] | [N ms] |

## 4. Test Types and Load Profiles
### 4.1 Load Test
- Virtual users: [N] concurrent
- Ramp-up: [X] min to [N] users
- Steady state: [Y] min
- Ramp-down: [Z] min
- Think time: [T] s between actions

### 4.2 Stress Test
- Start at [N] users, increase by [step] every [interval]
- Continue until error rate exceeds [X %] or P95 exceeds [Y ms]

### 4.3 Spike Test
- Baseline: [N] users for [X] min
- Spike to [M] users over [S] seconds
- Hold spike for [H] min, then return to baseline

### 4.4 Soak Test
- Virtual users: [N] concurrent
- Duration: [H] hours
- Monitor: memory, connection pools, disk, GC pauses

## 5. Environment
- **Topology**: [Description of test environment]
- **Parity gaps**: [Known differences from production]
- **Test data**: [Volume and seeding strategy]
- **External deps**: [Mocked / sandboxed / live]

## 6. Tooling
- **Load generator**: [Tool name and version]
- **Monitoring**: [APM, infrastructure dashboards]
- **Log aggregation**: [Tool name]

## 7. Execution Schedule
| Run | Type | Date/Sprint | Owner |
|-----|------|-------------|-------|
| 1 | Baseline load | [Date] | [Name] |

## 8. Risks and Mitigations
| Risk | Impact | Mitigation |
|------|--------|-----------|

## 9. Exit Criteria
- All acceptance criteria met during steady state
- No resource exhaustion trends in soak test
- Results reproducible across two consecutive runs
```

## Inline Example — Condensed Plan

Below is a condensed example for a REST API order service. See `./examples/api-performance-plan.md` for a fully detailed version.

```markdown
# Performance Test Plan: Order Service API v2.4

## Objectives
Validate that the Order Service meets SLA targets under Black Friday
peak traffic (3x normal) after the v2.4 database migration.

## Acceptance Criteria
| Metric | Target | Critical |
|--------|--------|----------|
| P95 response time | < 250 ms | < 600 ms |
| Throughput | 1200 req/s | 900 req/s |
| Error rate | < 0.05 % | < 0.5 % |
| CPU | < 65 % | < 85 % |

## Load Profile — Peak Load Test
- 800 virtual users, ramp 0 → 800 over 10 min
- Steady state: 30 min at 800 users
- Think time: 2 s (normal distribution, σ = 0.5 s)

## Critical Transactions
| Transaction | Weight | Target P95 |
|-------------|--------|-----------|
| POST /orders | 30 % | 300 ms |
| GET /orders/{id} | 45 % | 150 ms |
| GET /orders?status=pending | 15 % | 400 ms |
| DELETE /orders/{id} | 10 % | 200 ms |

## Tooling
- Load generator: k6 v0.50 (scripts in repo under /perf/k6/)
- Monitoring: Datadog APM + Grafana infra dashboards
- Alerting: PagerDuty silenced for perf-test environment
```

## Common Pitfalls

Warn users about these mistakes and actively check for them in the generated plan:

1. **Unrealistic load profiles** — Using constant request rates with zero think time creates traffic patterns that never occur in production. Always include think time and realistic user journeys.
2. **Testing in the wrong environment** — Performance results are meaningless if the test environment differs significantly from production. Document every parity gap and assess its impact.
3. **Skipping warm-up** — JIT compilation, connection pool initialization, and cache priming cause the first minutes of a test to be unrepresentative. Include a warm-up phase and exclude it from metrics.
4. **No baseline** — Without a baseline run, there is no way to tell whether a change improved or degraded performance. Always run a baseline before making changes.
5. **Ignoring resource saturation** — Passing response-time targets while CPU sits at 95 % means the system has no headroom. Always include resource utilization criteria.
6. **Testing only the happy path** — Include error scenarios, authentication failures, and cache misses in the load mix.
7. **Measuring averages instead of percentiles** — Averages hide tail latency. Always report P50, P95, and P99.
8. **No reproducibility check** — A single passing run is not sufficient. Require at least two consecutive runs with consistent results.

## Key Metrics Reference

The following metrics must appear in every plan. See `./references/metrics.md` for detailed definitions and collection guidance.

| Category | Metrics |
|----------|---------|
| Latency | P50, P95, P99 response time; time to first byte (TTFB) |
| Throughput | Requests per second; transactions per minute; bytes per second |
| Errors | HTTP error rate; application error rate; timeout rate |
| Resources | CPU utilization; memory utilization; disk I/O; network I/O |
| Saturation | Thread pool usage; connection pool usage; queue depth; GC pause time |

## Tool Selection Quick Guide

Match tooling to context. See `./references/tools.md` for a comprehensive comparison.

- **k6**: Best for developer-owned performance testing in CI/CD. JavaScript scripting, low resource footprint, excellent CLI experience.
- **JMeter**: Best for teams that prefer GUI-based test design or need broad protocol coverage (JDBC, JMS, LDAP).
- **Gatling**: Best for high-throughput HTTP scenarios in Scala/Java/Kotlin ecosystems. Strong DSL and reporting.
- **Locust**: Best for Python teams wanting full programmatic control over load shapes.
- **Artillery**: Best for testing serverless, microservice, and event-driven architectures with minimal configuration.

## Related Skills

- **load-profile-builder** — Design realistic load profiles from production traffic patterns or business projections.
- **stress-test-scenario-builder** — Create stress test scenarios that systematically find breaking points.
- **soak-test-checklist** — Checklist for long-duration soak tests that detect slow resource leaks.
- **failover-test-planner** — Plan failover and resilience tests that validate behavior during component failures.

## Bundled References

- `./references/tools.md` — Detailed performance testing tool comparison
- `./references/metrics.md` — Performance metrics definitions and collection guidance
- `./examples/api-performance-plan.md` — Complete example plan for a REST API service
