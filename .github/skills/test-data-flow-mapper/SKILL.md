---
name: test-data-flow-mapper
description: 'Map and trace data flows across system boundaries for integration test design. Use when understanding how data moves between services, identifying data transformation points that need testing, tracing data lineage for test verification, or finding data integrity risks at integration points.'
---

# Test Data Flow Mapper

Map and trace data flows across system boundaries to identify integration test targets.

## When to Use

- When understanding how data moves between services
- When identifying data transformations that need testing
- When tracing data lineage for test verification
- When finding data integrity risks at integration points

## Procedure

1. Identify data entities that flow across boundaries
2. For each entity, trace the flow:
   - **Source**: Where the data originates
   - **Transformations**: How the data changes at each step
   - **Destinations**: Where the data ends up
   - **Validation points**: Where data is validated or constrained
3. Identify risk points:
   - Type conversions (string→int, date format changes)
   - Encoding/decoding (character sets, serialization formats)
   - Aggregation/splitting (one-to-many, many-to-one)
   - Default values and null handling
   - Truncation (field length mismatches)
4. Recommend test assertions at each transformation point

## Output Format

```markdown
# Data Flow Map: [Entity/Process]

## Flow Diagram
[Source] → [Transform 1] → [Service B] → [Transform 2] → [Destination]

## Transformation Points
| Step | Input | Transformation | Output | Risk | Test Assertion |
|------|-------|---------------|--------|------|----------------|

## Data Integrity Risks
| Risk | Location | Impact | Test Scenario |
|------|----------|--------|---------------|
```
