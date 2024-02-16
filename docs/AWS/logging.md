---
title: AWS Logging & Event Streams
date: 2024-02-16
---

## CloudWatch Events / EventBridge

- If x happens, or at Y time(s) do z
- There is default Event bus (A stream of events) for the account
- In CloudWatch Events there is only 1 event bus available
- EventBridge can have additional event buses
- Rules match incoming events(based on patterns) or schedules

!!! note

    EventBridge is replacing CloudWatch Events