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

## AWS CloudTrail

- Enabled by default. (only available for 90 days). Default means data is not written to S3.
- `Trails` can be configured to send data to CloudWatch & S3.
- Cloudtrail includes management events only by default. Data events cost extra & need to be enabled.
- Most services log events to their region. But global services such as IAM, STS, CloudFront log data as Global Service Events to `us-east-1`
- Cloudtrail is **Not Realtime** - There is a delay.