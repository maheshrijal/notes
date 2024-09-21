---
title: NOSQL Databases & DynamoDB
date: 2023-04-02
---

# NOSQL Databases & DynamoDB

## DynamoDB

- NoSQL public Database as a Service - Key/Value & Document
- No self-managed servers or infrastructure
- Manual/Automatic provisioned performance IN/OUT or On-Demand
- Highly resilient across AZs and optionally global
- Really fast. Provides single digit millseconds latency
- Provides backups, point-in-time recovery and encryption at rest
- Event-Driven integration. Do things when data changes

### DynamoDB On-Demand Backups

- Manual snapshots retained until manually removed
- Restore is possible within the same region or cross region
- Adjust encryption settings with restore
- Customer is responsible for performing the backup & removing older backups

### DynamoDB Point-in-time Recovery

- Not enabled by default (Enabled on a table by table basis)
- Continuous record of changes allows to replay to any point (35 day window) - Restore within 1 second granularity

### DynamoDB considerations

- NOSQL - preference DynamoDB in the exam
- Relational DATA - generally not DynamoDB
- KEY/VALUE - preference DyanamoDB in the exam
- Access via console, CLI, API. **No SQL**
- Billed based on RCU, WCU, storage and features

### DynamoDB Indexes

- Query is the most efficient operation in a DyanmoDB but, it can only work on 1 PK value at a time & optionally a single or a range of sort key values
- Indexes are alternative views on table data
- Can choose which attributes from base table are projected for both indexes

#### Local Secondary Indexes

- Create a view with **different/altnernative Sort Key**
- Must be created with the base table itself
- Max **5 LSIs** per base table
- Shares the RCU and WCU with the table
- Attributes - ALL, KEYS_ONLY & INCLUDE

#### Global Secondary Indexes

- Create a view with **different Partition Key & Sort Key**
- Can be created at any time
- Default limit of 20 per base table
- Have their own RCU and WCU allocations
- Attributes - ALL, KEYS_ONLY & INCLUDE

**LSI & GSI Consideration**

- Careful with projection (KEYS_ONLY, INCLUDE, ALL)
- Queries on attributes not projected  are expensive
- Use GSIs as default, LSI only when strong consistency is required
- Use indexes for alternative access patterns

### DynamoDB Stream & Triggers


**Streams**

- Time ordered list of ITEM Changes in a table
- 24-Hour rolling window
- Enabled on a per table basis
- Records INSERTS, UPDATES, and deletes
- Different view types influence what is in the stream
- View Types: KEYS_ONLY, NEW_IMAGE, OLD_IMAGE, NEW_AND_OLD_IMAGES

**Trigger**

- ITEM changes generates an alerts
- That event contains the data which changed
- An action is taken using that data
- STREAMS + Lambda invocation (trigger)
- Used in reporting, analytics, aggregation, messaging or notifications

### DynamoDB Global tables

- Global tables provides multi-master cross-region replication
- Tables are created in multiple regions and added to the same global table
- Last writer wins is used for conflict resolution (most recent writer)
- Reads and Writes can occur to any region
- Generally sub-second replication between regions
- Strongly consistent reads ONLY in the samge region as writes (otherwise eventually consistent)

### DynamoDB Accelerator (DAX)

- Primary Node (Write) and Replicas (Read)
- Nodes are HA, Primary failure = election
- In-memory cache - Scaling. Much master reads, reduced costs
- Scale up & Scale Out
- Supports write-through (commit DynamoDB & write to cache)
- While DynamoDB is public AWS service, DAX is **deployed within a VPC**
- Reduce response time of reads operation
- Write heavy application do not benefit from DAX (Read heavy, with millisecond latency of read requirement do)

### DynamoDB TTL

- Timestamp for automatic DELETE of items
- When TTL is enabled on a table a specific attributeis selected for TTL
- A per partition processs periodically runs, checking the current time (in seconds since epoch) to the value in the TTL attribute
- ITEMS where the TTL attribute is older than the current time are set to expired
- Another per-partition background process scans for expired items and removes them from tables and indexes and a delete is added to streams if enabled.
- DELETE operatins caused by TTL are background system processes and don't impact table performance and they aren't chargeable

## Amazon Athena

- Serverless Interactive Queries Service
- Ad-hoc queries on data - pay only for data consumed
- **Schema-on-read** - table like translation
- **Original data never changed** - remains on S3
- Schema translates data => relational-like when read
- Output can be sent to other AWS services
- Tables are defined in advance in a data catalog and data is projected through when read. It allows SQL like queries on data without transforming source data
- Queries where **loading/transformation isn't desired**
- Occasional / Ad-hoc queries on data in S3
- Serverless querying scenarios - cost conscious
- **Querying AWS Logs** - VPC Flow Logs, CloudTrail, ELB Logs, cost reports etc
- AWS **Glue Data Catalog** & **Web Server Logs**
- **Athena Federated Query** can query other data source (Can query non S3 data sources)


## Amazon Redshift

- Petabyte scale Data Warehouse
- OLAP (column based) not OLTP (row/transactions)
- Pay as you use. Similar to RDS
- Direct Query S3 using **Redshift Spectrum**
- Direct Query other DB using **Federated Query**
- Server based (not serverless)
- **One-AZ** in a VPC - not HA
- **Leader Node** - Query input, planning & aggregation
- **Compute Node** - performing queries of data
- VPC security, IAM Permissions, KMS at rest encryption, CW monitoring
- Redshift **Enhanced VPC Routing** - by default uses public routes for traffic, but when Enhanced VPC Routing is enabled, traffic is routed based on VPC networking (SG, NACL, VPC Gateways)

### Redshift Resilience and Recovery

- **Automatic incremental backups to S3** occur every ~8 hours or 5 GB of data & by default have 1-day retention (configurable up to  365 days)
- Manual snapshots performed manually, stored in S3 and do not expire unless deleted manually
- Redshift backups into S3 protects against AZ failures
- Restoring from snapshots creates a brand new cluster
- Redshift can be configured to copy snapshots to another AWS region for DR - with a seperate configurable retention period