---
title: Amazon SQS
date: 2023-01-25
---

# Amazon SQS (Simple Queue Service)

- One of AWS oldest services
- Fully managed message queuing service (Application Decoupling)
- Unlimited throughput, unlimited number of messages in the queue
- Low latency (< 10ms on publish & receive)
- **Limitation of 256KB per message**

!!! note
    Default message retention period is 4 days, max is 14 days

- Can have duplicate messages (at-least-once delivery)
- Can have out-of-order messages (best effort ordering)