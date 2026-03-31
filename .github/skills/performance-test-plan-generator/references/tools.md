# Performance Testing Tool Comparison

Use this reference when recommending tooling in a performance test plan.

## Tool Overview

| Tool | Language | License | Protocol Support | Distributed | Cloud Options |
|------|----------|---------|-----------------|-------------|---------------|
| k6 | Go (scripts in JS/TS) | AGPL-3.0 | HTTP/1.1, HTTP/2, WebSocket, gRPC | Yes (native) | Grafana Cloud k6 |
| JMeter | Java | Apache 2.0 | HTTP, HTTPS, FTP, JDBC, JMS, LDAP, SOAP, TCP | Yes (controller/worker) | BlazeMeter, OctoPerf |
| Gatling | Scala/Java | Apache 2.0 | HTTP, WebSocket, JMS, MQTT | Yes (Gatling Enterprise) | Gatling Enterprise Cloud |
| Locust | Python | MIT | HTTP (extensible via plugins) | Yes (native master/worker) | Custom (K8s, cloud VMs) |
| Artillery | Node.js | MPL-2.0 | HTTP, WebSocket, Socket.io, gRPC, Kinesis | Yes (Artillery Pro) | Artillery Pro Cloud |

## Detailed Comparison

### k6

**Best for**: Developer-centric teams, CI/CD pipeline integration, API load testing.

**Pros**:
- Written in Go — single binary, low resource footprint on load generators.
- JavaScript/TypeScript scripting is accessible to most developers.
- First-class CLI experience; runs headlessly with no GUI dependency.
- Built-in support for thresholds, checks, and custom metrics.
- Native integration with Grafana for real-time dashboards.
- Excellent documentation and active community.

**Cons**:
- No GUI test recorder or visual test builder.
- Browser-based testing (k6 browser) is still maturing.
- gRPC and WebSocket support require explicit module imports.
- AGPL license may be a concern for some organizations.

**Use when**: The team writes code, performance tests live in the repo, and CI/CD automation is a priority.

---

### JMeter

**Best for**: Teams needing GUI-based test design or broad protocol coverage.

**Pros**:
- Mature ecosystem with hundreds of plugins.
- GUI test plan editor lowers the barrier for non-developers.
- Widest protocol support of any open-source tool (JDBC, LDAP, JMS, FTP, SOAP).
- Large community; extensive tutorials and Stack Overflow answers.
- Distributed testing via controller/worker architecture.

**Cons**:
- JVM-based — higher memory consumption per virtual user.
- XML-based test plans are hard to version-control and review in PRs.
- GUI becomes unwieldy for complex test plans.
- Thread-per-user model limits scalability per load generator.
- Reporting requires plugins or external tools for production-quality dashboards.

**Use when**: The team prefers visual test design, needs non-HTTP protocols, or has existing JMeter expertise.

---

### Gatling

**Best for**: High-throughput HTTP testing in Scala/Java/Kotlin ecosystems.

**Pros**:
- Asynchronous, non-blocking architecture handles very high user counts efficiently.
- Powerful Scala DSL for expressive, maintainable test scripts.
- Excellent built-in HTML reports with detailed percentile breakdowns.
- Code-based tests integrate well with version control and CI/CD.
- Java and Kotlin DSLs available for teams not using Scala.

**Cons**:
- Steeper learning curve for teams unfamiliar with Scala.
- Protocol support narrower than JMeter (HTTP, WS, JMS, MQTT).
- Distributed testing requires Gatling Enterprise (commercial).
- Community edition lacks some enterprise reporting features.

**Use when**: The project is JVM-based, throughput requirements are very high, and the team values code-as-tests.

---

### Locust

**Best for**: Python teams wanting full programmatic control.

**Pros**:
- Pure Python — leverage any Python library (data generation, ML-based load shaping).
- Custom load shapes via the `LoadTestShape` class.
- Built-in web UI for real-time monitoring during test runs.
- Simple master/worker distributed mode.
- Very easy to get started; minimal boilerplate.

**Cons**:
- Python's GIL can limit per-process throughput; relies on gevent for concurrency.
- Native support is HTTP only; other protocols require custom clients.
- Reporting is basic compared to Gatling or k6 Cloud.
- Less structured than DSL-based tools for large test suites.

**Use when**: The team is strong in Python, needs custom load shapes, or wants to embed ML/data-science logic in tests.

---

### Artillery

**Best for**: Serverless, microservice, and event-driven architectures.

**Pros**:
- YAML-based test definitions are concise and easy to read.
- Native support for HTTP, WebSocket, Socket.io, gRPC, and AWS Kinesis.
- Lightweight Node.js runtime; easy to run in containers or serverless functions.
- Plugin system for custom protocols and integrations.
- Artillery Pro adds distributed cloud execution and dashboards.

**Cons**:
- YAML-only config can be limiting for complex conditional logic (JS hooks help).
- Community edition has basic reporting; Pro required for advanced analytics.
- Smaller community than JMeter or k6.
- Node.js single-thread model requires careful resource management at high scale.

**Use when**: The stack is Node.js-heavy, architectures are event-driven, or quick YAML-based setup is preferred.

## Decision Matrix

Answer these questions to narrow down the choice:

| Question | If Yes | If No |
|----------|--------|-------|
| Does the team write JavaScript/TypeScript? | k6, Artillery | Gatling, Locust, JMeter |
| Is CI/CD integration a hard requirement? | k6, Gatling, Artillery | JMeter (possible but harder) |
| Are non-HTTP protocols needed (JDBC, JMS, LDAP)? | JMeter | Any tool |
| Is the team primarily Python? | Locust | Other tools |
| Is a GUI test builder needed? | JMeter | Other tools |
| Is very high throughput per node critical? | Gatling, k6 | Locust, JMeter |
| Is the architecture event-driven / serverless? | Artillery | Other tools |
