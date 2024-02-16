---
title: AWS Serverless Services
date: 2024-02-15
---

## AWS Lambda

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


## Amazon SNS
- Pub/Sub messaging service
- Public AWS service - network connectivity with AWS public Zone
- Coordinates the sending and delivery of messages
- Messages are `<=256KB` payloads
- SNS topic are the base entity of SNS and topics have permissions & configuration
- A publisher sends messages to a Topic
- Topics have subscribers which receive messages (HTTP/HTTPS endpoints, Email addresses, SQS queues, Mobile push notifications, SMS Messages & Lambda)
- SNS used across AWS for notifications - Eg: CloudWatch alarms & Cloudformation stack change alerts
- Offers delivery status for (HTTP, Lambda, SQS)
- Delivery retries
- HA & Scalable (Regionally resilient)
- Server side encryption (SSE)
- Cross-Account via Topic Policy


## AWS Step Functions

- Step functions have a serverless workflow (Start -> States -> END)
- States are things which occur inside state machine
- Maximum duration - 1 year
- Standard Workflow(Default - max 1 year execution limit) and Express Workflow (High volume event processing workloads - max )5 minute execution
- Started via API Gateway, IOT Rules, EventBridge, Lambda, Manual
- Can use a template to create & export state machines - Amazon States Language (ASL) - JSON template
- IAM Role used for permissions

### States

- SUCCEED & FAIL - either succeed or fail
- WAIT - wait for a period of time or until date & time
- CHOICE - take a different path depending on input
- PARELLEL - create parellel branches
- MAP - accepts a list of things & performs action based on the list
- TASK - a single unit of work performed by state machine. Integrated with (Lambda, Batch, DynamoDB, ECS, SNS, SQS, GLUE, SageMaker, EMR, Step Functions)


## API Gateway

- Create and manage APIs
- Endpoint/entry-point for applications
- Sits between applications & integrations (services)
- Highly available, scalable, handles authorization, throttling, caching, CORS, transformations, OpenAPI spec, direct integration and much more
- Can connect to services/endpoints in AWS or on-premises
- HTTP, REST & WebSocker APIs
- CloudWatch logs can store and manage full stage request and response logs. CloudWatch can store metrics for client and integration rules
- API Gateway cache can be used to reduce the number of calls made to backend integrations and improve client performance

### Endpoint Types

1. Edge Optimzed
    - Requests routed to nearest CloudFront POP (POint of presense)
2. Regional endpoint - used when clients are in same region
3. Private endpoint - Endpoint accessible only within a VPC via a interface endpoint

### Stages

- APIs are deployed to stages, each stage has one deployment
- Each stage has it's own endpoint URL & settings
- Stages can be enabled for canary deployments. If done, deployments are made to the canary not the stage
- Stages enabled for canary deployments can be configured so a certain percentage of traffic is sent to the canary. This can be adjusted over-time or the canary can be promoted to make it the new base `stage`


### Errors

- `4xx` - Client Error - Invalid request on client side
- `5xx` - Server Error - Valid request, backend issue
- `400` - Bad Request - Generic
- `403` - Access Denied - Authorizer denies or WAF filtered
- `429` - API Gateway throttle - Client has exceeded a configured throttling amount
- `502` - Bad Gateway - bad output returned by lambda
- `503` - Service Unavailable - backing endpoint offline or major service issue
- `504` - Integration failure/timeout(Dault: `29S`) - If lambda is providing backing compute for API gateway then the API gateway request will return `504` after `29s`

### Caching

- Caching is defined per stage for API Gateway
- Cache TTL default is `300s` configurable: `0s - 3600s`
- Cache can be encrypted
- Cache size 500 MB to 237 GB


## Amazon SQS (Simple Queue Service)

- Public, Fully Managed, Highly Avaialable Queues - Standard or FIFO
- **Limitation of 256KB per message** - link to object for large data
- Received messages are hidden (VisibilityTimeout)
- If the client does not delete message when processing is finsihed, the message reappears
- Dead-Letter queues can be used for problematic messages
- ASGs can scale & Lambdas invoke based on queue length

<!-- - One of AWS oldest services
- Fully managed message queuing service (Application Decoupling)
- Unlimited throughput, unlimited number of messages in the queue
- Low latency (< 10ms on publish & receive)
- **Limitation of 256KB per message**

!!! note
    Default message retention period is 4 days, max is 14 days

- Can have duplicate messages (at-least-once delivery)
- Can have out-of-order messages (best effort ordering) -->
