---
name: schema-diff-impact-analysis
description: 'Analyze the impact of schema changes (API, database, message) on consumers and tests. Use when API schemas change and you need to know what breaks, when database migrations affect dependent services, when message format changes impact consumers, or when assessing breaking vs non-breaking changes.'
---

# Schema Diff Impact Analysis

Analyze the impact of schema changes on consumers, tests, and dependent systems.

## When to Use

- When API schemas change and you need to assess the blast radius
- When database schema migrations affect dependent services
- When message or event formats change
- When determining if a change is breaking or non-breaking

## Procedure

1. Identify the schema change (before and after versions)
2. Classify the change type:
   - **Added field** (usually non-breaking for consumers that ignore unknown fields)
   - **Removed field** (breaking if any consumer uses it)
   - **Renamed field** (breaking)
   - **Type changed** (breaking)
   - **Required → optional** (may affect validation)
   - **Optional → required** (breaking for existing producers)
   - **Default value changed** (potentially breaking)
   - **Enum values changed** (added is usually safe, removed is breaking)
3. Identify all consumers of the schema
4. For each consumer, assess:
   - Does the consumer use the changed fields?
   - Will existing data break under the new schema?
   - Do existing tests cover the changed behavior?
5. Recommend migration strategy and test updates

## Output Format

```markdown
# Schema Change Impact Analysis

## Change Summary
| Field | Change Type | Breaking? | Affected Consumers |
|-------|-----------|----------|-------------------|

## Consumer Impact
| Consumer | Impact | Required Action | Test Updates |
|----------|--------|----------------|-------------|

## Migration Recommendations
- [Steps to safely migrate]

## Test Updates Required
- [Tests that need modification]
```
