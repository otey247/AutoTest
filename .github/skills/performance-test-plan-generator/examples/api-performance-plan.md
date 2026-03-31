# Performance Test Plan: Order Service REST API v2.4

## 1. Overview

- **System under test**: Order Service v2.4, deployed on Kubernetes (3 replicas, 2 vCPU / 4 GB each), backed by PostgreSQL 15 (primary + read replica) and Redis 7 cache.
- **Test objective**: Validate that the Order Service meets SLA targets under projected Black Friday peak traffic (3x normal daily peak) following the v2.4 database migration.
- **Business context**: Black Friday launch is scheduled for November 29. The v2.4 release includes a schema migration that adds a composite index and denormalizes order-line lookups. Stakeholders require evidence that performance has not regressed and the system handles 3x peak.

## 2. Acceptance Criteria

| Metric | Target | Critical Threshold | Measurement Window |
|--------|--------|--------------------|-------------------|
| P50 response time | < 100 ms | < 200 ms | Steady state |
| P95 response time | < 250 ms | < 600 ms | Steady state |
| P99 response time | < 500 ms | < 1500 ms | Steady state |
| Throughput | 1200 req/s sustained | 900 req/s minimum | Steady state |
| Error rate (5xx) | < 0.05 % | < 0.5 % | Full run |
| Error rate (4xx, non-auth) | < 0.01 % | < 0.1 % | Full run |
| CPU utilization (app pods) | < 65 % | < 85 % | Steady state |
| Memory utilization (app pods) | < 70 % | < 85 % | Steady state |
| PostgreSQL connection pool | < 70 % | < 90 % | Steady state |
| Redis hit rate | > 85 % | > 70 % | Steady state |

## 3. Critical Transactions

| # | Transaction | Method & Path | Weight | Target P95 | Notes |
|---|------------|---------------|--------|-----------|-------|
| 1 | Create order | POST /api/v2/orders | 25 % | 300 ms | Writes to DB, publishes event |
| 2 | Get order by ID | GET /api/v2/orders/{id} | 35 % | 120 ms | Cached in Redis (TTL 5 min) |
| 3 | List orders (filtered) | GET /api/v2/orders?status=pending&page=1 | 20 % | 400 ms | Paginated, DB query |
| 4 | Update order status | PATCH /api/v2/orders/{id}/status | 10 % | 250 ms | Writes, invalidates cache |
| 5 | Cancel order | DELETE /api/v2/orders/{id} | 5 % | 200 ms | Soft delete, publishes event |
| 6 | Health check | GET /api/v2/health | 5 % | 50 ms | Lightweight, used by LB |

## 4. Test Types and Load Profiles

### 4.1 Baseline Load Test

Validate performance under normal daily peak traffic.

- **Virtual users**: 300 concurrent
- **Ramp-up**: 0 → 300 users over 5 minutes
- **Steady state**: 20 minutes at 300 users
- **Ramp-down**: 300 → 0 over 3 minutes
- **Think time**: 2 seconds (normal distribution, σ = 0.5 s)
- **Expected throughput**: ~400 req/s

### 4.2 Peak Load Test (Black Friday 3x)

Validate performance under projected Black Friday peak.

- **Virtual users**: 800 concurrent
- **Ramp-up**: 0 → 800 users over 10 minutes
- **Steady state**: 30 minutes at 800 users
- **Ramp-down**: 800 → 0 over 5 minutes
- **Think time**: 2 seconds (normal distribution, σ = 0.5 s)
- **Expected throughput**: ~1200 req/s

### 4.3 Stress Test

Find the breaking point beyond Black Friday projections.

- **Start**: 800 concurrent users (Black Friday peak)
- **Step increase**: +100 users every 3 minutes
- **Continue until**: Error rate exceeds 1 % OR P95 exceeds 1500 ms OR throughput stops increasing
- **Think time**: 2 seconds
- **Maximum**: 2000 users (safety cap)

### 4.4 Spike Test

Simulate a flash sale or viral social media moment.

- **Phase 1 — Baseline**: 300 users for 5 minutes
- **Phase 2 — Spike**: Ramp to 1500 users over 30 seconds
- **Phase 3 — Hold**: 1500 users for 5 minutes
- **Phase 4 — Drop**: Return to 300 users over 30 seconds
- **Phase 5 — Recovery**: 300 users for 5 minutes
- **Acceptance**: Error rate under 2 % during spike; P95 under 2 s; full recovery within 2 minutes of drop

### 4.5 Soak Test

Detect slow leaks over extended duration.

- **Virtual users**: 400 concurrent (slightly above normal peak)
- **Duration**: 4 hours
- **Think time**: 3 seconds
- **Monitor**: Memory RSS trend, PostgreSQL connection count, Redis memory, pod restart count, GC pauses
- **Acceptance**: No upward trend in memory; zero pod restarts; connection pool stable

## 5. Test Environment

### Infrastructure

| Component | Test Environment | Production | Parity Gap |
|-----------|-----------------|------------|------------|
| App pods | 3 replicas, 2 vCPU / 4 GB | 3 replicas, 2 vCPU / 4 GB | None |
| PostgreSQL | db.r6g.xlarge, 100 GB data | db.r6g.xlarge, 480 GB data | Data volume lower in test |
| Redis | cache.r6g.large, single node | cache.r6g.large, single node | None |
| Load balancer | ALB, same config | ALB, same config | None |
| Network | Same VPC, different subnet | Production subnet | Minimal difference |

### Test Data

- **Orders**: 2 million pre-seeded orders across 50,000 users.
- **Seeding method**: SQL dump restored from anonymized production snapshot (2 weeks old).
- **User accounts**: 10,000 test users with valid auth tokens (rotated before each run).
- **Product catalog**: Full production catalog (15,000 SKUs) replicated to test DB.

### External Dependencies

| Dependency | Strategy | Notes |
|------------|----------|-------|
| Payment gateway | Stubbed (WireMock) | Returns 200 with 50 ms simulated latency |
| Inventory service | Sandbox instance | Shared sandbox; rate limit 500 req/s |
| Notification service | Stubbed (WireMock) | Fire-and-forget; response not measured |
| Auth service (JWT validation) | Live test instance | Tokens pre-generated; no login flow in test |

## 6. Tooling

### Load Generation

- **Tool**: k6 v0.50
- **Scripts location**: `/perf/k6/order-service/`
- **Execution**: CLI via `k6 run --vus 800 --duration 30m order-load.js`
- **Output**: JSON results streamed to InfluxDB

### Monitoring and Observability

| Purpose | Tool | Dashboard |
|---------|------|-----------|
| Application metrics | Datadog APM | "Order Service — Perf Test" |
| Infrastructure | Grafana + Prometheus | "K8s Cluster — Perf Env" |
| Database | pganalyze | "PostgreSQL — Perf" |
| Cache | Redis Insight | "Redis — Perf Env" |
| Log aggregation | Datadog Logs | Filter: `env:perf-test AND service:order-service` |

### Alerting

- PagerDuty alerts **silenced** for the `perf-test` environment during test windows.
- Slack channel `#perf-test-runs` receives automated k6 summary posts.

## 7. Execution Schedule

| Run # | Test Type | Target Date | Owner | Prerequisites |
|-------|-----------|-------------|-------|---------------|
| 1 | Baseline load | Nov 11 | @perf-team | Data seeded, monitoring verified |
| 2 | Peak load (3x) | Nov 12 | @perf-team | Baseline passing |
| 3 | Stress test | Nov 13 | @perf-team | Peak load passing |
| 4 | Spike test | Nov 14 | @perf-team | Peak load passing |
| 5 | Soak test | Nov 15 | @perf-team | All short-duration tests passing |
| 6 | Regression (repeat #2) | Nov 18 | @perf-team | Any fixes deployed |

### Pre-Test Checklist

- [ ] Test environment deployed and matching target configuration
- [ ] Test data seeded and verified (row counts, referential integrity)
- [ ] Monitoring dashboards accessible and showing live data
- [ ] PagerDuty alerts silenced for perf-test environment
- [ ] WireMock stubs deployed and verified
- [ ] k6 scripts reviewed and committed to repo
- [ ] Warm-up run completed (5 min at 50 users, results discarded)

## 8. Risks and Mitigations

| # | Risk | Likelihood | Impact | Mitigation |
|---|------|-----------|--------|-----------|
| 1 | Shared sandbox for Inventory service rate-limited | Medium | Test throughput capped | Pre-arrange increased limits or stub if needed |
| 2 | Test DB has less data than production | Low | Query plans may differ | Verify EXPLAIN plans match production for critical queries |
| 3 | Network noise from other test-env tenants | Low | Latency variance | Schedule tests during off-hours; compare multiple runs |
| 4 | v2.4 migration introduces lock contention | Medium | Elevated P99, timeouts | Monitor pg_stat_activity for locks; have rollback plan ready |
| 5 | Redis cache cold start after data reseed | Low | Initial latency spike | Run warm-up phase; exclude first 5 min from metrics |

## 9. Exit Criteria

The performance test cycle is considered **passed** when:

1. All acceptance criteria in Section 2 are met during the steady-state window of the Peak Load Test (Run #2 or #6).
2. Stress test identifies a breaking point above 1.5x the Black Friday target (above 1800 req/s).
3. Spike test shows recovery to baseline metrics within 2 minutes of spike drop.
4. Soak test shows no upward memory trend, zero pod restarts, and stable connection pools over 4 hours.
5. Results are reproducible across two consecutive peak load runs with less than 10 % variance in P95.
6. No critical or high-severity defects remain open from performance test findings.

## 10. Reporting

After each run, produce a summary containing:

- Test type, date, duration, and k6 script version (git SHA)
- Acceptance criteria pass/fail table
- Time-series charts: latency (P50/P95/P99), throughput, error rate, CPU, memory
- Comparison against baseline (Run #1) and previous runs
- Anomalies observed with timestamps and possible root causes
- Recommendations (pass, conditional pass, fail with required action)

Final report delivered to `#perf-test-runs` Slack channel and attached to the release ticket.
