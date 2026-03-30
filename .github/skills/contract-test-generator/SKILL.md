---
name: contract-test-generator
description: 'Generate contract tests that verify producer and consumer expectations match on an interface. Use when preventing breaking changes across services, creating Pact-style consumer-driven contracts, validating schema compatibility, or testing API contract compliance between teams.'
---

# Contract Test Generator

Generate contract tests that verify producer and consumer expectations match on shared interfaces.

## When to Use

- When services need contract verification between teams
- When preventing breaking API changes
- When implementing consumer-driven contract testing (e.g., Pact)
- When validating schema compatibility between producer and consumer

## Procedure

1. Identify the producer (API/service provider) and consumer (client/caller)
2. Define the consumer's expectations:
   - Which endpoints does the consumer call?
   - What request format does the consumer send?
   - What response fields does the consumer actually use?
   - What error responses must the consumer handle?
3. Generate consumer-side contract tests that:
   - Define expected interactions (request → response pairs)
   - Verify the consumer can handle the expected response shape
   - Test error handling for expected error responses
4. Generate provider-side verification tests that:
   - Verify the provider can fulfill each consumer contract
   - Check that response shapes match consumer expectations
   - Validate all required fields are present
5. Identify potential breaking changes:
   - Removed fields that consumers depend on
   - Changed field types or formats
   - New required request fields
   - Changed error response formats

## Output Format

Generate contract test files following the project's contract testing framework (Pact, Spring Cloud Contract, etc.) or produce contract definitions that can be used for verification.
