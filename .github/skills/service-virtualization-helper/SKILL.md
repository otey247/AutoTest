---
name: service-virtualization-helper
description: 'Set up service virtualization (mocks, stubs, simulators) for integration testing when real services are unavailable. Use when external APIs are not available in test environments, when creating test doubles for third-party services, or when simulating specific service behaviors for testing.'
---

# Service Virtualization Helper

Set up service virtualization for integration testing when real dependencies are unavailable.

## When to Use

- When external APIs are unavailable in test environments
- When third-party services need to be simulated
- When testing specific failure modes that are hard to reproduce
- When isolating components for focused integration testing

## Procedure

1. Identify which services need virtualization and why:
   - Not available in test environment
   - Too expensive to call in tests
   - Need to simulate specific responses or failures
   - Need deterministic behavior for consistent test results
2. Choose the virtualization approach:
   - **Record and replay**: Capture real responses, replay in tests
   - **Static stubs**: Predefined request/response mappings
   - **Dynamic simulation**: Logic-based response generation
   - **Contract-based**: Generate from API spec or contract
3. Define the virtual service behavior:
   - Request matching rules (URL, method, headers, body patterns)
   - Response templates (status, headers, body)
   - Delay simulation for timeout testing
   - Error responses for failure testing
   - Stateful behavior if needed (sequence of responses)
4. Recommend tools based on the technology stack:
   - WireMock (Java/HTTP), MockServer, Mountebank (polyglot)
   - nock (Node.js), responses (Python), httpmock (Go)
   - Pact stub server (contract-based)

## Output Format

```markdown
# Service Virtualization Plan

## Services to Virtualize
| Service | Reason | Approach | Tool |
|---------|--------|----------|------|

## Virtual Service Definitions
### [Service Name]
- **Base URL**: [mock URL]
- **Mappings**:
  - [Method] [Path] → [Status] [Response description]
  - [Error scenario] → [Error response]
```
