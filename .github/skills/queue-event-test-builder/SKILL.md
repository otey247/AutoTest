---
name: queue-event-test-builder
description: 'Build test scenarios for asynchronous message-based integrations including queues, events, and pub/sub systems. Use when testing message queue consumers and producers, validating event-driven architectures, testing message ordering and idempotency, or verifying dead letter queue handling.'
---

# Queue/Event Test Builder

Build test scenarios for asynchronous message-based integrations and event-driven architectures.

## When to Use

- When testing message queue producers and consumers
- When validating event-driven architecture flows
- When testing message ordering, idempotency, and delivery guarantees
- When verifying dead letter queue and error handling

## Procedure

1. Identify the messaging patterns in use:
   - Point-to-point queues (SQS, RabbitMQ)
   - Pub/sub topics (SNS, Kafka, EventBridge)
   - Event streams (Kafka, Kinesis)
2. For each message flow, design test scenarios:
   - **Happy path**: Message published, consumed, processed correctly
   - **Duplicate message**: Same message delivered twice (idempotency)
   - **Out-of-order**: Messages arrive in unexpected sequence
   - **Malformed message**: Invalid schema or missing fields
   - **Consumer failure**: Processing fails mid-way
   - **Poison message**: Message that always fails (dead letter)
   - **Backpressure**: Consumer can't keep up with production rate
   - **Timeout**: Message not consumed within expected time
3. Define assertions for each scenario:
   - Message was published with correct schema
   - Consumer processed and produced expected side effects
   - Failed messages reached dead letter queue
   - Retry behavior worked correctly

## Output Format

```markdown
# Queue/Event Test Scenarios: [Flow Name]

## Message Flows
| Producer | Topic/Queue | Consumer | Message Type |
|----------|------------|----------|-------------|

## Scenarios
### [Scenario Name]
- **Category**: [Happy path / Duplicate / Ordering / Error / Poison]
- **Setup**: [Test data and preconditions]
- **Action**: [What to do]
- **Assertions**: [What to verify]
- **Priority**: [High / Medium / Low]
```
