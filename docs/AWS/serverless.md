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


## Amazon SQS

- Public, Fully Managed, Highly Avaialable Queues - Standard or FIFO
- **Limitation of 256KB per message** - link to object for large data
- Received messages are hidden (VisibilityTimeout)
- If the client does not delete message when processing is finsihed, the message reappears
- Dead-Letter queues can be used for problematic messages
- ASGs can scale & Lambdas invoke based on queue length

!!! note

    SNS topic can be placed before SQS topics in an application architecture so that the same message can be sent to different SQS queues based on the requirement. (**FANOUT Architecture**)
    Eg:

        1. Video uploaded to S3
        2. SNS triffers a notification to 3 different SQS queues(FANOUT)
        3. The 3 different SQS queues send the message to worker pools to transcode the video based on quality (1080p, 720p, 480p)

- Short polling (immediate) vs Long (waitTimeSeconds) polling
- Ecnryption at rest (KMS) & in transit
- Access to queue is based on identity policies or queue policies(Queue policy is just like resource policy & can allow access from external account only)

### Standard Queues

- At-least-once delivery, no guarantee on order of messages
- Scalabale, as wide as required Near Unlimited TPS
- Best-Effort Ordering, no rigid prevervation of message order
- Use Case: Decoupling worker pools, batch for future processing

### FIFO Queues

- Ecxactly once delivey, guaranteed ordering
- 3000 messages per second with batching or up to 300 messages per second without batching
- FIFO queues will have a `.fifo` suffix

### Billing

- Billed on `requests`
- 1 request can receive `1-10` messages up to 64 KB total

### Delay Queues

- Postpone delivery of messages to consumers
- Delay Queue has a `DelaySeconds` set. Messages added to the queue will be **invisible for DelaySeconds** (default 0, max 15 minutes)
- `DelaySeconds` must be value higher than 0 for queue to be a Delay Queue
- Message timers allow a **per-message invisibility** to be set, overriding any queue setting. MIN=0, Max=15 (Not supported on FIFO queues)

### Dead=Letter Queues

- `ReceiveCount` attribute is incremented every time a message is received in the Queue
- **redrive-policy** specifies the Source Queue, the Dead-Letter Queue and the conditions where messages will be moved from  one to the other (Defined via maxReceiveCount)
- When ReceiveCount > maxReceiveCount & message isn't deleted, it's moved to the dead-letter queue
- Enqueue-timestamp(time when message was introduced to the queue) is unchanged when message movied to DLQ. Retention period of a DLQ is generally longer than the source queue.


## Kinesis Data Streams

- Kinesis is scalable streaming service (designed to ingest data)
- Producers send data to kinesis stream
- Streams can scale from low to near infinite data rates
- Public service & HA by design
- Streams store a **24-hour** moving winodw of data (Data older than 24H is discarded)
- Window can be increased to a max of **365 days (additional cost)**
- Multiple producers can send data & multiple consumers can access data from the moving window
- Kinesis uses a **Shard Architecture**. Shards are added to the stream as required. Each **shard provides 1MB ingestion & 2MB of consumption per second**
- Data is stored on a stream using Kinesis Data Record (Max: 1MB)

Amazon Kinesis Data Streams enables real-time processing of streaming big data. It provides ordering of records, as well as the ability to read and/or replay records in the same order to multiple Amazon Kinesis Applications. The Amazon Kinesis Client Library (KCL) delivers all records for a given partition key to the same record processor, making it easier to build multiple applications reading from the same Amazon Kinesis data stream (for example, to perform counting, aggregation, and filtering).

AWS recommends Amazon Kinesis Data Streams for use cases with requirements that are similar to the following:

- Routing related records to the same record processor (as in streaming MapReduce). For example, counting and aggregation are simpler when all records for a given key are routed to the same record processor.

- Ordering of records. For example, you want to transfer log data from the application host to the processing/archival host while maintaining the order of log statements.

- Ability for multiple applications to consume the same stream concurrently. For example, you have one application that updates a real-time dashboard and another that archives data to Amazon Redshift. You want both applications to consume data from the same stream concurrently and independently.

- Ability to consume records in the same order a few hours later. For example, you have a billing application and an audit application that runs a few hours behind the billing application. Because Amazon Kinesis Data Streams stores data for up to 365 days, you can run the audit application up to 365 days behind the billing application.


### SQS vs Kinesis

**SQS**

- 1 production group, 1 consumption group
- Decoupling & Asynchronous communications
- No persistence of messages, no concept of window

**Kinesis**

- Designed for huge scale ingestion
- Support multiple consumers & a rolling window
- Data ingestion, Analytics, Monitoring, App clicks, Mobile click streams

### Amazon Data Firehose

- Fully Managed service to load data for data lakes, data store and analytics services
- Persists data beyond the rolling window of kinesis data streams
- Automatic scaling fully serverless & resilient
- Supports transformation of data on the fly using lambda (can add more latency)
- Billed on data volume through firehose
- FireHose can accept data from producers or obtain data fom Kinesis data stream
- FireHose offers near realtime delivery i.e. delivery when buffer size amounts to 1MB or buffer interval passes 60 seconds
- Firehose can transform data using Lambda & write to HTTP, Splunk, ElasticSearch and S3. For writing directly to RefShift, it uses an intermediate S3 bucket.

Valid Destination for  Data FireHose: HTTP, Splunk, Redshift, ElasticSearch, S3

Use case: Persistence for data coming into kinesis stream, Store data in different format

!!! warning

    Kinesis is real-time, delivery (~60 seconds) & Data Firehose in near real time

### Kinesis Data Analytics

- Real time processing of data using **SQL**
- Ingests from Kinesis Data Streams or FireHose
- Destinations: FireHose (S3, RefShift, ElasticSearch & Splunk)[], AWS Lambda, Kinesis Data Steams
- RealTime for AWS Lambda, Kinesis Data Stream, Near Real Time for FireHose
- Pay for data proceessed

- Use cases:
    - Streaming data needing real-time SQL processing
    - Time-series analytics elections/e-sports
    - Real-time dashboard - leaderboards for games
    - Real-time metrics - Security & Response for teams

### Kinesis Video Streams

- Ingest live video data from producers
- Producers: Security Cameras, SmartPhones, cars, drones, time-serialized audio, thermal, depth and RADAR data
- Consumers can access data frme-by-frame or as needed
- Can persist and encrypt (in-transit & at rest) data
- **CANNOT access data ingested by Kinesis data streams from any external service/storage**. Can be accessed via APIs only
- Integrates with other AWS Service eg: Rekognition and Connect
- Kinesis data stream(Output by Rekognition) receives analysis data of the video, and vs the face collection identifying any Detected or Matched faces

## Amazon Cognito

- Provides Authentication, Authorization & user management for web/mobile apps

### User Pools

- Sign-in get a JSON Web Token (JWT) [Most AWS Services do not accept JWT]
- USer directory management and profiles, sign up & sign in (customizable UI), MFA and other features
- Provide social sign in using identities provided by facebook, google, apple as well as SAML providers
- **User Pool tokens cannot be used to access AWS resources**
- USer Pool token used as a proof of authentication
- User Pool Tokens can grant access to APIs via Legacy lambda custom authorizers and now directly via API Gateway

### Idenitity Pools

- Exchange a external identity for a set of temporary AWS credentials
- Allow you to offer access to Temporary AWS Credentials
- Unauthenticated Identities - Guest access to AWS resources
- Federated Identities - SWAP Google, Facebook, twitter SAML2.0 and **User Pool** for short term AWS credentials to access AWS resources
- Cognito assumes an IAM Role defined in Identity Pool and returns temporary AWS credentials that can be used to access AWS resources

!!! note

    The swapping of any external ID provider token for AWS Credemtials is known as Web Identity Federation


## AWS Glue

- Serverless ETL (Extract, Transform & Load)
- There as a AWS service called datapipeline (Which can do ETL) but, it uses servers in the AWS Account (EMR)
- Moves and transform data between source and destination
- Crawls data source and generates the AWS Glue Data catalog
- Data Sources: Stores: S3, RDS, JDBC Compatible, DynamoDB
- Data Sources: Streams: Kinesis Data Stream & Apache Kafka
- Data Targets: S3, RDS, JDBC Databases

### Data Catalog

- Persistent metadata about data sources in a AWS region
- One catalog per region per account
- Avoids data silos
- Amazon Athena, RedShift Spectrum, EMR & AWS Lake formation all use Data Catalog
- Data is discovered by configuring crawlers for data sources and givin them credentials
- Crawlers connect to data stores, determine schema and create metadata in the data catalog
- Data Catalog can be used with AWS Glue to perform ETL

## Amazon MQ

- SNS & SQS are AWS services which utilize AWS APIs (Public Service, Highly Scalable, AWS Integrated)
- Open-source message broker
- Based on Managed Apache ActiveMQ
- Protocols: **AMQP, MQTT, OpenWire & STOMP**
- Provides QUEUES and TOPICS
- Provides One-to-One or One-to-Many messaging architecture
- Provides a Single Instances (Testing/Dev for cheap) or HA Pair (Active/Standby)
- VPC Based service - **Not a Public Service** - Private Networking is required
- No AWS native integrations

!!! tip

    Use SNS or SQS if AWS Integration is required (logging, permissions, encryption, service integration)

## Amazon AppFlow

- Fully-Managd **Integration** service (Middleware)
- Exchange data between applications (connectors) using flows
- Sync data across applications or aggregate data from different sources
- Uses Public endpoints, but works with PrivateLink to access private sources
- AppFlow connections can use Custom Connector SDK (build your own connector)
- Cnnections store configuration & credentials to access applications
- Connections can be reused across many flows
- Eg: Contact records from salesforce => Refshit
- Eg: Support ticket from Zendesk => S3
- Eg: Slack => Redshift