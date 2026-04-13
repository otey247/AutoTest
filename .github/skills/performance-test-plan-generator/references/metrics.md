# Performance Metrics Guide

Use this reference when defining acceptance criteria and monitoring requirements in a performance test plan.

## Metric Categories

### 1. Latency (Response Time)

Latency measures how long the system takes to respond to a request.

| Metric | Definition | Why It Matters |
|--------|-----------|----------------|
| **P50 (Median)** | 50th percentile response time — half of requests are faster | Represents the typical user experience |
| **P95** | 95th percentile — 95 % of requests are faster | Captures the experience of most users, including slower ones |
| **P99** | 99th percentile — 99 % of requests are faster | Reveals tail latency that affects 1 in 100 requests |
| **P99.9** | 99.9th percentile | Important for high-traffic systems where 0.1 % is thousands of users |
| **Min / Max** | Fastest and slowest observed response | Max is useful for detecting outliers; min confirms baseline |
| **TTFB** | Time to first byte from the server | Isolates server processing time from network transfer |
| **Standard deviation** | Spread of response times | High deviation signals inconsistent performance |

**Collection guidance**:
- Always report P50, P95, and P99 at minimum.
- Never rely on averages alone — they hide tail latency.
- Measure from the client side (load generator) to include network latency.
- Separate metrics by transaction type; aggregated latency masks slow endpoints.

---

### 2. Throughput

Throughput measures the volume of work the system handles per unit time.

| Metric | Definition | Typical Unit |
|--------|-----------|-------------|
| **Requests per second (RPS)** | HTTP requests completed per second | req/s |
| **Transactions per second (TPS)** | Business transactions completed per second | txn/s |
| **Transactions per minute (TPM)** | Same as TPS but per minute; useful for lower-volume systems | txn/min |
| **Bytes per second** | Data transfer rate | MB/s or Gbps |
| **Messages per second** | For queue-based or event-driven systems | msg/s |

**Collection guidance**:
- Report throughput during the steady-state window only.
- Track both achieved throughput and target throughput side by side.
- If throughput plateaus while virtual users increase, the system has reached saturation.
- Monitor throughput per endpoint to identify bottleneck APIs.

---

### 3. Error Rate

Error metrics quantify how often the system fails to serve requests correctly.

| Metric | Definition | Target Range |
|--------|-----------|-------------|
| **HTTP error rate** | Percentage of responses with 4xx/5xx status codes | Typically under 0.1 % for load tests |
| **Application error rate** | Percentage of logically incorrect responses (wrong data, partial responses) | Should be 0 % |
| **Timeout rate** | Percentage of requests exceeding a timeout threshold | Under 0.01 % |
| **Connection error rate** | Failed TCP connections, resets, refused connections | Should be 0 % under normal load |
| **Retry rate** | Percentage of requests that required retries | Indicates upstream instability |

**Collection guidance**:
- Distinguish between client errors (4xx) and server errors (5xx).
- Track error rate over time — a rising error rate during steady state signals degradation.
- Correlate errors with resource utilization to identify causes.
- Include timeout thresholds in the test plan (e.g., 30 s global timeout).

---

### 4. Resource Utilization

Resource metrics show how much infrastructure capacity the system consumes.

| Metric | Definition | Warning Threshold | Critical Threshold |
|--------|-----------|-------------------|-------------------|
| **CPU utilization** | Percentage of CPU capacity in use | 70 % | 90 % |
| **Memory utilization** | Percentage of RAM in use (RSS for containers) | 75 % | 90 % |
| **Disk I/O** | Read/write operations per second or MB/s | Varies by disk type | Near device limits |
| **Network I/O** | Inbound/outbound bandwidth utilization | 60 % of NIC capacity | 80 % of NIC capacity |
| **Disk space** | Available disk for logs, temp files, data | Under 80 % used | Under 90 % used |

**Collection guidance**:
- Monitor all tiers: load balancers, application servers, databases, caches.
- For containers, track both container-level and node-level resources.
- Capture metrics at 10-second intervals minimum during tests.
- Compare against baseline values, not just absolute thresholds.

---

### 5. Saturation

Saturation metrics reveal whether the system is running out of a specific resource.

| Metric | Definition | What It Indicates |
|--------|-----------|------------------|
| **Thread pool usage** | Active threads / max threads | Application concurrency limit |
| **Connection pool usage** | Active connections / max pool size | Database or service connection limit |
| **Queue depth** | Number of pending items in a work queue | Processing cannot keep up with intake |
| **GC pause time** | Duration and frequency of garbage collection pauses (JVM, .NET, Go) | Memory pressure causing stop-the-world events |
| **File descriptor usage** | Open file descriptors / OS limit | Socket or file handle exhaustion |
| **Swap usage** | Bytes of swap memory in use | Physical memory exhausted |
| **Load average** | System load average (1 min, 5 min, 15 min) | Overall system saturation |
| **Event loop lag** | Delay in processing event loop ticks (Node.js) | Main thread blocked |

**Collection guidance**:
- Saturation metrics are the earliest warning of impending failure.
- In soak tests, plot saturation metrics over time — even small upward trends matter.
- Set alerts at 80 % of pool/thread limits.
- GC metrics are critical for JVM and .NET applications; include heap usage and GC frequency.

## Metric Collection Checklist

Include the following in every performance test plan:

- [ ] P50, P95, P99 response time per transaction
- [ ] Overall and per-endpoint throughput (req/s)
- [ ] HTTP error rate (4xx and 5xx separately)
- [ ] Timeout rate
- [ ] CPU and memory utilization per tier
- [ ] Database connection pool utilization
- [ ] Thread pool or goroutine count
- [ ] GC pause time and frequency (JVM/.NET/Go)
- [ ] Disk and network I/O
- [ ] Queue depths for async processing

## Reporting Best Practices

1. **Use time-series charts** — Show metrics over the duration of the test, not just summary numbers.
2. **Overlay load profile** — Plot virtual users or RPS alongside latency to correlate cause and effect.
3. **Separate phases** — Clearly mark ramp-up, steady-state, and ramp-down in charts. Evaluate acceptance criteria against steady-state only.
4. **Compare against baseline** — Show the previous run or production baseline side by side.
5. **Highlight anomalies** — Call out latency spikes, error bursts, or resource jumps with annotations.
6. **Include raw data** — Attach CSV or JSON exports for reproducibility and deeper analysis.
