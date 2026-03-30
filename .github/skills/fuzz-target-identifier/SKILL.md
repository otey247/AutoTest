---
name: fuzz-target-identifier
description: 'Identify code and endpoints that are good candidates for fuzz testing. Use when deciding where to apply fuzzing, identifying parsers and input handlers that should be fuzzed, selecting API endpoints for fuzz testing, or prioritizing fuzzing targets by risk.'
---

# Fuzz Target Identifier

Identify code and endpoints that are good candidates for fuzz testing.

## When to Use

- When deciding where to apply fuzzing
- When identifying parsers and input handlers that should be fuzzed
- When selecting API endpoints for fuzz testing
- When prioritizing fuzzing targets by risk

## Procedure

1. Identify high-value fuzz targets:
   - **Parsers**: JSON, XML, CSV, custom format parsers
   - **Deserializers**: Object deserialization, protocol decoders
   - **Input handlers**: File upload processors, form handlers
   - **API endpoints**: Endpoints accepting complex payloads
   - **Protocol handlers**: Custom network protocols, message parsers
   - **Crypto functions**: Key handling, signature verification
   - **String processors**: Regex engines, template processors
2. Assess each target for fuzz-worthiness:
   - Handles untrusted input
   - Has complex parsing logic
   - Written in memory-unsafe language (higher priority)
   - Security-critical function
   - Previously had vulnerabilities
3. Recommend fuzzing approach:
   - Coverage-guided fuzzing (libFuzzer, AFL)
   - API fuzzing (RESTler, Schemathesis)
   - Property-based testing (Hypothesis, fast-check)
4. Prioritize by risk and feasibility

## Output Format

```markdown
# Fuzz Targets: [System]

## Identified Targets

| Target | Type | Risk | Approach | Tool | Priority |
|--------|------|------|----------|------|----------|
| [Parser/Endpoint] | [Parser/API/Handler] | [H/M/L] | [Coverage/API/Property] | [Tool] | [H/M/L] |

## Recommended Fuzzing Plan
1. [Highest priority target and approach]
2. [Next target]
```
