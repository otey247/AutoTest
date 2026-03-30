---
name: component-test-generator
description: 'Generate component tests that validate a cohesive feature slice with its local collaborators. Use when testing a module or service with mocked external dependencies, validating feature slices before integration, or testing beyond individual units but within a single component boundary.'
---

# Component Test Generator

Generate component tests that validate a cohesive feature slice with its local collaborators.

## When to Use

- When testing a module or service as a cohesive unit
- When validating feature slices with mocked external dependencies
- When testing broader behavior than unit tests but within component boundaries

## Procedure

1. Identify the component boundary — what is the feature slice being tested?
2. Identify external dependencies that should be mocked vs internal collaborators that should be real
3. Design test scenarios that exercise the component's public interface:
   - Complete feature workflows within the component
   - Error handling and edge cases at the component level
   - State management and side effects
4. Set up appropriate mocks, stubs, or fakes for external dependencies
5. Generate tests following project conventions

## Output Format

Generate component test files that:
- Test the component through its public interface
- Mock external dependencies (databases, APIs, message queues) appropriately
- Cover complete feature workflows within the component
- Validate state changes and side effects
- Follow project test file organization and naming conventions
