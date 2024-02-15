---
title: AWS Lambda
date: 2024-02-15
---

# AWS Lambda

- Function as a service (FaaS) - short running & focused workloads
- Functions use a runtime (eg: python 3.8)
- Functions are laoded & run in a runtime environment
- The environment has a direct memory (indirect CPU) allocation
- You are billed for the duration that function runs
- A key part of serverless architecture
- Functions can run for `900s` ~ 15 minutes
- Your directly control the memory allocated for Lambda functions whereas vCPU is allocated indirectly

!!! warning

    Docker is not suported as a Lambda runtime

Use cases:

- Serverless applications
- File processing
- Database Triggers
- Serverless Cron
- Realtime Stream Data processing

### Public Lambda

- By default Lambda functions are given public networking.
- Can access public AWS services & the public internet.
- Public networking  offers the best performance because no customer specific VPC networking is required.
- Public lambda functions have no access to VPC based services unless public IPs are provided & security controls allow external access

### Private Lambda

- Lambda runs inside a private subnet in a VPC
- Lambda functions running in a VPC obey all VPC networking rules
- Cannot access external resources outside the VPC unless access is allowed from the VPC(NatGW and Internet Gateway are required for VPC Lambdas to access internet resources)
- VPC Endpoints can proide access to public AWS services

### Security

- Lambda **execution roles** are IAM role attached to Lamdba functions which controls permissions the lambda function receives
- Lambda **resources policy** controls what service and accounts can invoke lambda functions. (Can be changed only via CLI & API)

### Logging

 - Lambda uses CloudWatch, CloudWatch Logs & X-Ray
 - Logs from Lambda executions goes into CloudWatch Logs
 - Metrics - invocation success/failure, retries, latency are stred in CloudWatch
 - Lambdas can be integrated with X-Ray for distributed tracing
 - To write logs into CloudWatch Logs Lambda require permissions via Execution Role

### Invocation


1. Syncronous invocation

    - CLI/API invoke a lambda function, passing in data & wait for a response -> Lambda function responds with data or fails
    - Client communicates via APIGW which is proxied to lambda function -> The lambda function respons or fails and the response is sent back to client via APIGW
    - Result (Success/Failure) is returned during the request
    - Errors or Retries have to be handled within the client

2. Asynchornous invocation

    - Typically used when AWS services invoke lambda functions
    - Eg: S3 buckets with S3 events enabled, on a new image upload will send a event to Lambda(S3 is not waiting for any kind of response)
    - If processing of the event fails, lambda will retry between 0 & 2 times (configurable). Lambda handles the retry logic
    - The lambda function needs to be idempotent. Reprocessing a same result should have the same end state.
    - Events can be sent to dead letter queues (DLQ) after repeated failed processing.
    - Lambda supports destinations (SQS, SNS, Lambda, & EventBridge) where successfull or failed events can be sent

 3. Event Source mappings

    - Typically used on streams or queues which don't support event generation to invoke lambda (Kinesis, DynamoDB streams, SQS)
    - Event source mapping uses permissions from the lambda exection role to interact with the event source
    - SQS queues or SNS topics can be used for any discared failed event batches.

### Versions

- Lambda functions have versions - v1, v2, v3
- A version is the code + the configuration of the lambda
- It's immutable - it never changes once published & has it's own ARN
- `$Latest` points at the latest version
- Alises (DEV, STAGE, PROD) point at a version - can be changed

### Startup Times

- An execution context is the environment a lambda function runs in. A cold start is a full creation & configuration including function code download. (Cold Start)
- A lambda invocation can reuse an exection context but has to assume it can't. If used infrequently contexts will be reomved. Concurrent executions will use multiple (potentially new) contexts.
- With Warm start, the same exectiion context is reused. A new event is passed in but the exection context creation can be skipped.
- Provisioned concurrency can be used. AWS will create & keep X contexts warm and ready to use improving start speeds.