---
title: AWS RDS
date: 2024-02-15
---
# Relational Database Service (RDS)

- RDS is a Database server as a Service (DBSaaS). We can run multiple databases on a DB Server
- RDS is a managed service. We don't have access to OS or SSH access except in RDS custom which does provide some low level access

## RDS Cost Parameters

- Instance Size & Type
- Multi AZ or not
- Storage type & amount
- Data transferred
- Backups & Snapshots
- Licensing (applicable on using commercial DB types)

## Multi AZ - Instance deployments

- Primary instance is configured to replicate data synchronously to a standby replica in another AZ
- Replication is at the storagte level
- Accesses to data is provided through database CNAME which points to primary instance
- All accesses read/write will occur from the primary instance
- On failover (which can be done manually for testing) the database CNAME will then point to the secondary instance (60-120s for failover)
- Instance deployment provide **1 standby replica only**
- Multi AZ can be within the same region
- Database backups can be taken from standby to improve performance
- Failover Scnearios: AZ Outage, Primary Failure, Manual failover, instance type change and software patching

## Multi AZ - Cluster deployments

- 1 writer instance can replicate to 2 reader instances (different AZs)
- Runs on much faster hardware: Graviton + local NVME SSD (Fast writes to local storage --> Flushed to EBS)
- Replication is done via transaction logs which is more efficient
- Reader instances can be utilized, but only for read transactions
- **Cluster Endpoint**: Points at the writer instance. used for read, write & administration
- **Reader Endpoint**: Points any reads at an available reader instance
- **Instance Endpoint**: Point at a specific instnace. Generally used for testing/fault finding
- Failover is faster `~35s`+transaction logs apply
- Writes are "committed" when atleast 1 reader instance has confirmed


## RDS Backups

The 2 types of backups for RDS: **Automated Backups** and **Snapshots** are stored in AWS. But they are stored in AWS managed buckets & not visible in S3.

!!! note

    There is a IO pause & performance impact when backups & snapshots are taken in a single AZ deployment. In multi AZ deployments the operation is performed from standby instances.

### Snapshot

- First Snap is FULL size of consumed data then it is incremental
- Snapshots don't expire (Even when RDS instance is deleted)

### Automated Backups

- Occur once per day
- First backup is FULL size then it is incremental
- Transaction logs are also written to S3 **every 5 minutes**
- Backups are automatically cleared by AWS. This can be configured from  `0-35` days (0 means disabled, maximum is 35 days)
- When deleting database automated backups can be retained but they still expire based on based on the retention period settings.

### Cross-Region backups

- RDS can replicate backups(both snapshots & transaction logs) to another region
- Charges apply for cross-region data copy & storage used in the destination region
- **NOT DEFAULT**: Cross-Region backups must be configured in automated backups

### Restores

- Creates a new RDS instance when you restore a automated backup or snapshot - NEW DNS Address
- **Restoring a manual SNAPSHOT** means restoring in a single point in time (snapshot creating time)
- **Restoring a automated backup** means restoring any 5 minute point in time
- Backups are restored & transcations logs are ***replayed*** to bring DB to desired point in time (provides a low RPO)
- Restoring aren't fast - Long RTO for a large database

### Read Replica

- Read only replicas of an RDS instance.
- Read Replicas are not part of the main database. They have seperate endpoint address
- Kept in sync using **asynchronous replication** so reads are eventually consistent
- Replicas can exist in same region as primary or in other region (Cross region read replicas)
- Can create up to **5 Read Replicas per database instance**
- Read Replicas can have their own read-Replicas - but lag starts to be a problem
- Read replicas provide gloabl performance improvements for reads - great for global availablility improvement & resilience
- Read Replicas offer `near 0 RPO`
- Replicas can be promoted quickly to their own DB - low RTO (Replicas are read only until promoted)
- Should be used for **Failure only** because data corruption would affect the reader instance as well.

**Network Cost - Read Replica**

In AWS there's a network cost when data goes from one AZ to another. But, for RDS Read Replicas within the same region, there is no fee since it is a managed service. But for cross region replicas, it incurs a replication fee.


### RDS Security

- SSL/TLS in transit is available fo RDS (Can be mandatory)
- Encryption at rest(EBS Volume) is supported through KMS
- AWS or Customer Managed Key (CMK) generates data keys
- Data keys are used for encryption operations
- Storage, Logs, Snapshots & Replicas are encrypted
- Encryption cannot be removed once added
- RDS MSSQL & RDS Oracle support TDE (Transaparent Data Encryption) --> Encryption is handled with the DB engine
- RDS Orcale supoprts TDE using CLoudHSM (Much stronger key controls)

**IAM Authentication**

- RDS can be configured to use IAM User authentication against a database
1. Start with a RDS instance & create a local database user account configured to allow authentication using AWS authentication token
2. IAM Users & EC2 Role have policies attached that allow users or roles that map that IAM identity onto the local RDS cluster
3. Based on the policies a token with a 15 minute validity is generated. This token can be used to login to database user within RDS without requiring a password

!!! warning

    This is only authentication & not authorization. Permissions over the RDS database are still contoller by the permissions on the local database user.

### RDS Custom

- Provides access to opearting system & databse engine that runs RDS
- Can connect to the database host using SSH, RDP, Session Manager
- Supported for **MSSQL** & **Oracle**
- EC2 instances, EBS volumes and S3 buckets are visible in the AWS account for RDS Custom


## Amazon Aurora


### Architecture

- Proprietary database from AWS
- Aurora uses a cluster (A single primary instance + 0 or more replicas)
- Aurora does not use local storage. It uses shared cluster volume that is available to all compute instances within the cluster
- Faster provisioning, improved availability & performance
- Secondary replicas of aurora can serve as failover if primary instance fails and they can also be used for read operations(secondary instances are read replicas) during normal functioning of the cluster
- Storage grows automatically in incrememts of 10GB upto 128 TB (Storage is build on what's used)
- Data written to primary instance is synchronously replicated across all 6 storage nodes across AZs
- Replication is at the storage level i.e. no resources consumed on the instances/replicas
- Aurora can have 15 replicas(any can be failover targets) while MYSQL has 5 and the replication process is faster `sub 10 ms lag`
- Failover in Aurora is instantenous. It's HA by default.
- Replicas can be added & removed without requiring storage provisioning

### Cost

- No free tier option
- Aurora doesn't support micro instances
- Beyond RDS singleAZ (micro) aurora offers much better value
- Compute is charged hourly, billing is per second with a 10 minute minimum
- Storage is billed GB/month consumed, high watermark (maximum storage consumed in a month) and IO cost per request
- 100% of DB size in backips are included

### Restore, Clone & Backtrack

- Backups in Aurora work in the same way as RDS
- Restored will create a new cluster
- Backtrack(enabled at cluster level) can be used which allows for in-place rewinds to a previous point in time
- Fast clones makes a new database much faster than copying all the data (copy on write)


### Aurora Serverless

- Supports scalabale ACU (Aurora Capacity Units)
- Aurora Serverless cluster has a min & max ACU
- Cluster adjusts based on load and can even go to 0 & be paused
- Consumption billing per-second basis
- Same resilience as Aurora (6 copies across AZs)
- ACUS are allocated from a shared pool managed by AWS
- Connection intiated by a user connecting to Aurora Serverless goes through **Aurora Proxy Fleets**(Proxy fleets broker a connection between client & ACU)

Use cases for Aurora Serverless:

    - Infrequently used applications
    - New applications (unsure of the laod & size of database instances)
    - Variable workloads
    - Unpredictable worklaods
    - Development & test databases (DB can be paused when not in used)
    - Multi-tenant applications (where scaling is proportional to customer revenue )

### Aurora Global

- Replication can take `~1s` (1 way) and happens at the storage layer
- Replication has no impact on DB performance
- Secondary regions can have 16 replicas
- Promoting another region (for disaster recovery) has an RTO(Recovery Time Objective) of < 1 minute
- Currently MAX 5 secondary regions are supported

Use case:

- Cross-Region DR & Business Continuity
- Global read scaling - low latency performance improvements for read


### Multi-Master Write

**Default Aurora**

- Default Aurora mode is Single-Master(One Read/Write + 0 Read Only replicas)
- Cluster Endpoint is used for read/write and read endpoint used for load balancing reads
- Failover takes time - replica promoted to read/write

**Multi-Master**

- All instances are capable of read/write by default
- No load balanced cluster endpoint. Application can connect to one or both of the cluster endpoints
- Use case: Fault Tolerance
- Failover events can happen inside the application itself
- No disruption of traffic between application & DB in case of failover


## RDS Proxy

- Fully managed proxy for RDS - serverless, autoscaling, highly available (Multi-AZ) by default
- Allows apps to **pool & share DB connections** established with the DB (Proxy maintains a long term connection pool)
- Improve database efficiency by reducing the stress on DB resources (CPU/RAM). Also minimizes open connections & timeouts
- Reduces RDS & Auora failover time by up to 66% in case of failover
- Accessed via Proxy endpoint - no app changes in most cases
- Proxy can enforce SSL/TLS
- Enforce IAM authentication for DB, & securely store credentials in the AWS secrets manager
- RDS Proxy is never publicly accessible (Must be accessed from a VPC)
- Lambda can use RDS Proxy
- Abstracts failure away from your applications

When to use RDS proxy:

    - Application connection failing with too many connection errors
    - DB instances using T2/T3 (small/burst) instances
    - AWS Lambda - time saved per connection & reuse & IAM auth
    - Long running connections (SAAS apps) - low latency
    - Where resilicence to database failure is priority
    - RDS proxy can reduce time for failover and make it transparent to the application


## Database Migartion Service (DMS)

- A managed database migration service
- Runs a replication instance
- Source and destination endpoints point at source and target DBs
- One of endpoints must be running on AWS

Job Types:

1. Full Load:  One off migration of all data
2. Full Load + CDC: Migrates existing data & replicates any ongoing changes
3. CDC Only: Replicate only data changes

### Schema Conversion Tool (SCT)

- DMS does not support schema conversion but **Schema Conversion Tool (SCT)** can assist with Schema Conversion
- Convert from one database engine to another
- **SCT is not used for movement of database between compatible database engines**
- Works with OLTP DB (MySQL, MSSQL, Oracle) and OLAP (Terradata, Oracle, Vertica, Greenplum)

!!! info

    DMS can utilize snowball for large scale DB migrations with SCT.

    1. Use SCT to extract data locally & move to snowball device
    2. Ship the device back to AWS. They load onto an S3 bucket
    3. DMS migrates from S3 into the target store
    4. Change Data Capture (CDC) can capture changes & via S3 intermediary they are also written to the target database


<!-- ## Amazon Aurora

-
- **Postgres & MySQL** are supported as Aurora DB (Drivers work as if the DB is aurora/mysql)
- Aurora is `AWS Cloud Optimized` & claims 5x performance improvement over MySql on RDS & over 3x performance improvement over Postgres on RDS
-

- Costs 20% more than RDS but very efficient
 -->

<!--
RDS is a managed database service which uses SQL as the primary language. It allows to create following type of databases: `Postgres, MySQL, Maria DB, Oracle, Microsoft SQL Server, Aurora (AWS Proprietary Database)`

- Automated provisioning, OS patching
- Continous backup & restore specific timestamp (Point in time restore)
- Monitoring dashboards
- Read replicas for improved read performance
- Multi AZ setup for DR
- Mainteanance window for upgrades
- Scaling capacibility (Vertical & Horizontal)
- Storage backed by EBS (`GP2 or IO1`)

!!! warning
    You cannot SSH into a managed RDS database

## Storage Auto Scaling

Helps you increase storage on your RDS DB instance dynamically. When RDS detects you are running out of free storage, it scales automatically. You have to set a **Maximum Storage Threshold** (Maximum limit for DB storage). Supports all database engines.

Automatically modify storage if:

    - Free storage is less than 10% of allocated storage
    - Low-Storage lasts at least 5 Minutes
    - 6 hours have passed since last modification

Useful for applications with unpredictable workloads.

## Read Replica vs Multi AZ

### Read Replica

- Up to 5 Read Replicas
- Within AZ, Cross AZ or Cross Region
- Replication is **ASYNC** so reads are eventually consistent
- Replicas can be promoted to their own DB
- Application must update connection string to leverage read replica

**Network Cost - Read Replica**

In AWS there's a network cost when data goes from one AZ to another. But, for RDS Read Replicas within the same region, there is no fee since it is a managed service. But for cross region replicas, it incurres a replication fee.

### Multi AZ

RDS Multi AZ is benefical for Disaster Recovery.

- SYNC Replication
- One DNS name  - Automatic failover to standby instance
- Increased availability
- Failover in case of loss of AZ, loss of network or storage failure
- No manual intervention
- Not used for scaling, the standby DB is just for failover

!!! note
    The Read Replicas can be setup as Multi AZ for Disaster Recovery (DR)

## RDS - Single AZ to Multi AZ

- Zero downtime operation
- Just click on modify for DB & enable multi AZ

Internally a snapshot is taken & a new DB is restored from the snapshot in the target AZ. Then, synchronization is established between two databases.

## RDS Custom

Managed **Oracle & Microsoft SQL** Server Database with OS & database customization. Offers all the beneifts of RDS along with `underlying DB, OS, EC2 access`. Deactivate automation mode to to perform customization & it is better to take a DB snapshot beforehand.

## Amazon Aurora

- Proprietary database from AWS
- **Postgres & MySQL** are supported as Aurora DB (Drivers work as if the DB is aurora/mysql)
- Aurora is `AWS Cloud Optimized` & claims 5x performance improvement over MySql on RDS & over 3x performance improvement over Postgres on RDS
- Storage grows automatically in incrememts of 10GB upto 128 TB
- Aurora can have 15 Replicas while MYSQL has 5 and the replication process is faster `sub 10 ms lag`
- Failover in Aurora is instantenous. It's High Availability by default.
- Costs 20% more than RDS but very efficient

### HA & Scaling

- 6 Copies of data across 3 AZ
- 4 copies out of 6 for writes
- 3 copies out of 6 for reads
- Self healing with Peer to Peer Replication
- Storage is stripped across 100s of volumes
- 1 Aurora instance (master) takes writes
- Automatic failover (Less than 30 seconds)
- Master + upto 15 Read Replicas
- Support for cross region replication

### DB Cluster Endpoints

Aurora DB cluster has a **Writer Endpoint** which always points to the master.


AutoScaling can be setup on top of read replicas but this can make it very difficult for the application to track the ReadReplicas. For this there is a `Reader Endpoint`. It helps with load balancing & automatically connects to all read replicas. Load Balancing happens at the connection level.

Replica Autoscaling can be added based on Target Metric (Average CPU Utilization or Average connections)

!!! note
    Aurora provides `Backtrack` to restore data at any point  of time without using backups.


### Aurora Replicas - Autoscaling

If there are huge number of reads in our replicas. Replica autoscaling can setup additional reader endpoints after detecting the CPU load in the reader endpoints.

### Aurora - Custom Endpoints

- Some reader replica instances are larger than others (Instance Type).
- This is done to define a a subset of Aurora Instances as a Custom Endpoint.
- The Reader Endpoint is generally not used after defining Custom Endpoints.
- Eg Usage: Run analytical queries on specific replicas.

### Aurora - Serverless

- Automated database instantiation & auto-scaling based on actual usage.
- Good for infrequent intermittent or unpredicatable workloads.
- No capcity is planning is required and you `pay per second`.
- In the backend aurora manages a proxy fleet & clients talk to proxy fleet.

### Aurora - Multi-Master

- In case you want __immediate failover__ for writer node (HA).
- Every node does Read/Write - vs promoting a Read Replica as the new master.

### Global Aurora

**Aurora Cross Region Read Replicas**

- Useful for disaster recovery
- Simple to put in place

**Aurora Global Database (Recommended)**

- 1 Primary region (read/write)
- Up to 5 secondary (read-only) regions, replication lag is less than 1 second
- Upto 16 Read Replicas per secondary region
- Helps for decreasing latency
- Promoting another region (for disaster recovery) has an RTO(Recovery Time Objective) of < 1 minute.

!!! info
    Typicaly cross-region replicas takes `less than 1 second` with Aurora Global Database.

### Aurora Machine Learning

- Enables you to add ML-based predictions to your applications via SQL
- Simple, optimized & secure integration between aurora & AWS ML services
- Supported Services: Amazon SageMaker, Amazon Comprehend

Use Case: Fraud detection, ads targeting, sentiment analysis, product recommendations

## RDS Backups

**Automated Backups**

- Daily full backup of the database (During the backup windows)
- Transaction logs are backed-up by RDS every 5 minutes
- Ability to restore to any point in time (from oldest backup to 5 minute ago)
- 1 - 35 days of retention, set 0 to disable automated backups

**Manual DB Snapshots**

- Manually triggered by the user
- Retention of backup for as long as you want

!!! tip
    In a stopped RDS database, charges are still incurred for storage. If you plan on stopping for a long time, you should snapshot & restore to save cost.

## Aurora Backups

**Automated backups**

- 1 - 35 days (Cannot be disabled)
- Point in time recovery in that timeframe

**Manual DB Snapshots**

- Manually Triggered by the user
- Can be retained for as long as you want


## Restore Options - RDS & Aurora

- Restoring RDS/Aurora backups or snaphosts creates a new database

**Restoring MySQL RDS database from S3**

    - Create backup of your on prem DB
    - Store it on Amazon S3
    - Restore the backup file onto a new RDS instance running MySQL

**Restore MySQL Aurora Cluster from S3**

    - Create a backup of on-prem database using Percona XtraBackup
    - Store the backup file on Amazon S3
    - Restore the backup file onto a new Aurora cluster running MySQL

!!! note
    For restoring into RDS MySQL we just a need a DB backup, for restoring in Aurora MySQL we should take backup using Percona XtraBackup

## Aurora Database Cloning

- Create a new Aurora DB cluster from existing one
- Faster than snapshot & restore
- The new DB uses the same cluster volume and data as the original but will change when data updates are made
- Very fast & Cost Effective
- Useful to create a __staging__ database from a __production__ database without impacting the production database.

## Security - RDS & Aurora

**At-rest encryption**

- Database master & replicas encryption using AWS-KMS - must be defined at launch time
- If the master is not encrypted, the read replicas cannot be encrypted
- To encrypt an un-encrypted database, go through a DB snapshot & restore as encrypted

**In-Flight encryption**: TLS-ready by default, use the AWS TLS root certificates

**IAM Authentication**: IAM roles to connect to your database (instead of username/password)

**Security Groups**: Control network Access to your RDS / Aurora DB

**Audit Logs**: Can be enabled & sent to CloudWatch logs for longer retention

!!! tip
    RDS & Aurora do not have SSH access except on RDS Custom

## RDS Proxy

- Fully managed proxy for RDS
- Allows apps to **pool & share DB connections** established with the DB
- Improve database efficiency by reducing the stress on DB resources (CPU/RAM). Also minimizes open connections & timeouts
- Proxy is serverless, autoscaling, highly available (Multi-AZ)
- Reduces RDS & Auora failover time by up to 66% in case of failover
- No code change required for most apps
- Enforce IAM authentication for DB, & securely store credentials in the AWS secrets manager
- RDS Proxy is never publicly accessible (Must be accessed from a VPC)
- Lambda can use RDS Proxy -->