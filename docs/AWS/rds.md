---
title: AWS RDS
date: 2023-02-02
---
# Relational Database Service (RDS)

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
- Lambda can use RDS Proxy