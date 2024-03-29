---
title: AWS EC2
date: 2023-01-25
---
# AWS EC2 (Elastic Compute Cloud)

## AMI
An Amazon Machine Image (AMI) is a customization of an EC2 instance.

    - You add your own software, configuration, operating system, monitoring etc
    - Allows for faster boot time because software is pre-packaged
    - AMI is built for a specific region, but can be copied across regions (AMI ID changes on copy)

EC2 instances can be launched from:

    - A public AMI: AWS provided & maintained
    - Your own AMI: Self made & maintained
    - An AWS marketplace AMI: AMI provided / sold by someone else
    - AMI has permissions (Public, Your account, Specific Accounts)
    - AMI has a cost for the EBS snapshots that it refereces. (Billed for snapshot storage)

Process to build an AMI:

    - Start EC2 instance & customize it
    - Stop the instance (for data integrity)
    - Build an AMI - this creats an EBS snapshot
    - Launch instances from the AMI

!!! tip

    Creating an AMI from a configured instance + Application is known as **AMI Baking**

!!! warning

    AMI can't be edited. If edit is required launch instance > update configuration > create new AMI.

## EC2 Instance Types

### General Purpose - T

Workloads:
- Low cost, general purpose, web servers

### Compute Optimised - C

Workloads:
- Batch Processing
- Media transcoding
- High performance web servers
- High performance computing
- Scientific modeling & Machine learning
- Dedicated gaming servers

### Memory Optimised - R, X, HighMemory, Z

Workloads:
- High performance relational/non-relational databases
- Distributed cache
- In-memory databases optimised for BI (Business Intelligence)
- Applications performing real-time processing of unstructured data

### Storage Optimised - I, D, H

Workloads:
- High frequency OLTP systems
- Relational & NoSQL DB's
- Cache of in-memory DB's Eg: Redis
- Data warehousing applications
- Distributed file systems

## EC2 User Data

- EC2 user data is used to automate boot tasks such as Installing updates & Installing software.
- User data runs with root user.
- User data is not secure & therefore should not be used for passwords or long term credentials.
- Limited to 16 KB in size
- Can be modified when instance is stopped but user-data is only executed once at launch.

## Purchase Options

### On Demand Instance

- Pay a fixed rate per hour. Good for apps where compute needs scaling up/down

### Spot Instance

Instances can be extremely cheap but, can be terminated by AWS any time. Best for jobs that can be terminated at any time eg: batch processing

- Not charged for partial hour if terminated by AWS but, charged for full hour if terminated by user.
- Useful for batch jobs that are resilient to failure.

#### Spot Instance Requests

- Define a max spot price & get the instance while `current spot price < max`
- If `current spot price > max price` you can choose to stop or terminate your instance with a 2 minute grace period.

Types of Spot Requests:

1. One time Request (Instance launched as soon as spot request is fulfilled & request goes away)
2. Persistent Request (Spot instances are launched automatically if the instance gets terminated. Spot request remain active). To terminate persistent spot requests, first cancel the spot request then stop the request.

#### Spot Fleets

Spot fleet allow us to request spot instances with the lowest price. These are a set of Spot Instances + (Optional) On demand instances. Spot fleet will try to meet the target capacity with price constraints.

- Define possible launch pools: Instance type, OS, AZ
- Can have multiple launch pools, so the fleet can choose
- Spot fleet stops launching instances when max capcity or cost is met.

Strategies to allocate Spot instances:

- lowestPrice: from the pool with the lowest price (Cost optimization, short workload)
- diversified: distributed across all the pools (Great for Availability & long workloads)
- capacityOptimized: pool with optimal capacity for the number of instances.

### Reserved Instances

Fixed compute, resverved for a certain period of time. Cheaper than on-demand if used for predicatable long term. (1 or 3 years). You reserve a specific instance attribute (Type, Region, Tenancy, OS).
Instances can be reserved in a Zone or AZ. Unused instance can be sold in the marketplace.

Workload:

- Reserved Instances: long worload
- Convertible Reserved Intances: long worload with the ability to change instance types

### Savings Plan

Commitment to an amount of usage for 1 or 3 years. Suitable for long workloads. Commit to a certain type of usage.
Eg: (5$ per hour for 1 or 3 years.) Any usage beyond saving plan is billed On-Demand. Also, you're locked to a specific instance family & Region.

### Dedicated Host

Book an entire physical server. Physical EC2 server are available only to 1 customer. Used when regulatory requirements specify that you must not be using multi-tenant computing.

- Allows customer to address compliance requirements, use existing server-bound software licenses & control instance placement.
- Host hardware has physical sockets and cores
- Dedicated hosts can be purchased on-demand or reserved for 1/3 years
- Most expensive option
- Customer gets access to the physical server & visibility into lower level hardware
- No instance charges, you pay for the EC2 host

**Limitations**

- AMI Limits: RHEL, SUSE Linux and Windows AMIs are not supported
- Amazon RDS instances are not supported
- Placement groups are not supported
- Hosts can be shared with other ORG accounts using RAM (Resource Access Manager)

### Dedicated Instances

EC2 instances running on hardware dedicated to a single customer. Hardware can be shared with other instances in the same account & there is no control over instance placement.

- Own instance on your own hardware

## Capacity Reservation

Planning in advance for capacity

Workload: Suitable for short term uninterrupted worloads in a specific AZ

## Placement Groups

Allow control over where EC2 instances are placed in AWS infrastructure. Following strategies are available for Placement groups.

1. Cluster
    - Clusters EC2 instances in a low latency zone in a single AZ (Same rack)
    - High performance but low availability (AZ is locked when the first instance is launched)
    - Use case: Big data job that needs to complete fast or Application that needs extremely low latency & high network throughput
    - It is a best practice to choose the same type of instance & launch all instances in cluster placement group at once
    - Can span VPC peers but it impacts performance

2. Spread
    - All EC2 instances are on different hardware across different AZ
    - Reduced risk of simultaneous failure
    - **Limited to 7 instances** per AZ per placement group
    - Use case: Critical application that needs to maximize availability or critical applications where instances must be isolated from failure.
    - Spread placement group is not supported for Dedicated Instances or Hosts

3. Partition
    - Spread instances across partitions (Different set of racks) within an AZ.
    - Upto 7 partitions per AZ & Scales to 100s of EC2 instances per group
    - Instances are isolated from rack failure (Instances in a rack do not share instances in other partitions)
    - Can span across multiple AZs in the same region
    - EC2 instances get access to parition using the metadata
    - Instances can be placed in a specific partition or they can be auto places by EC2
    - Use case: Large scale parellel processing, Big data , Hadoop, Kafka, Cassandra
    - Use case: Topology aware applications to contain the impact of failure to part of an application

## Elastic Network Interfaces - ENI

ENI is a logical networking component in a VPC that represents a virtual network card. ENI's are bound to specific AZ. It can include the following attributes:

- A primary private IPv4 & one or more secondary IPv4 address from the IPv4 address range of your VPC
- One Elastic IP address (IPv4) per private IPv4 address
- One public IPv4 address
- One or more IPv6 addresses
- One or more security groups
- A MAC address

ENI can be moved from one instance to another (Failover)

ENIs can be created independently from EC2 instances.

## EC2 Hibernate

- The in-memory (RAM state) is preserved
- The instance Boot is much faster
- Under the hood (The RAM state is written to a  file in the root EBS volume)
- The root EBS volume must be encrypted & must contain enough space
- Available for on-demand, spot & reserved instances

Use case: Long running processes, saving the RAM state, best use for services that take time to initalize

Limitations: Instance RAM size must be less than 150 GB, does not work on bare metal instances, Instance cannot be hibernated for more than 60 days

## EC2 Instance Storage

### EBS Volume

EBS (Elastic Block Store) volume is a network drive that is attached to EC2 instance while they run. It allows for data persistence even after instance termination.

- Some EBS volume support multi attach (Can be attached to multiple EC2 instances.)
- EBS volumes are bound to a specific AZ
- In the free tier 30GB of EBS storage is allowed per month
- Uses the network to communicate to the instance (Latency)
- Can be detached from a instance & attached to another one quickly
- Have provisioned capacity (Size in GB & IOPS)
- EBS volumes have a delete on termination option when creating EC2 instances. (Delete on termination is enabled on root volume by default but not on EBS volume)

!!! warning "ONE AZ"

    EBS strage is provisioned in **ONE AZ** and it is reslient in that AZ.

#### EBS Snapshot

Snapshots allows to create a backup of EBS volume. It is not necessary to detach a volume to take a snapshot, however, it is recommended. This snapshot can be used to create another EBS volume in another region or AZ(Snapshot must be copied to that region after creation).

 - Snapshots are incremental volume copies to S3
 - The first copy is a full copy of `data` on the volume
 - Future snapshots are incremental
 - Volumes can be created (restored) from snapshots
 - Snapshots can be copied to other AWS region

 **EBS Snapshots/Volume Performance Considerations**

- New EBS Volume - Full performance immediately'
- Snpshots restore lazily - fetched gradually
- Requested blocks are fetched from S3 immediately (But performance is low)
- Force a read of all data immediately (using DD) (Better performance)
- Recycle bin for EBS snapshot(1 day to 1 year): Rules can be setup to retain deleted snapshots so that they can be recovered after accidental deletion.
- **Fast snapshot Restore (FSR)**: Force full initialization of snapshot & have no latency on first use. This feature is expensive($$$) but can be useful for large snapshots. (Upto 50 FSR per region - this is set on the snapshot & region. Eg: 1 snapshot restoring to 3 AZs is 3 snapshot restore sets)
- EBS Snapshot Archive: Move snapshot to an archive tier (75% cheaper). Takes 24 - 72 hours to to restore the archive.

#### EBS Volume Types

EBS Volumes are categorized in `size`, `iops`, `throughput`.

- GP2 / GP3 (SSD): **General Purpose** – general purpose, balances price and performance.
- IO1 / IO2 (SSD): Highest performance - Mission critical low latency or high throughput
- ST1 (HDD): Low cost HDD volumes designed for frequently accessed, throughput intensive workloads
- SC1 (HDD): Lowest cost HDD volume designed for less frequently accessed workloads

!!! warning
    For EC2 instances only `GP2/GP3` & `IO1/IO2` can be used as boot volumes.

**General Purpose SSD(GP2/GP3):**

- Cost effective, low latency
- Used for system boot volumes, Virtual desktops, dev & test env
- Size can very betwwen 1 GiB - 16 TiB

GP3:

    - Newer generation:
    - Baseline of 3000 iops & throughput of 125 MiB
    - Can increase iops upto 16000 & throughput up to 1000 MiB `independently`

GP2:

    - Small GP2 volumes can burst IOPS to 3000
    - Size of the volume & IOPS are linked, max IOPS is 16000
    - 3 IOPS per GB, means at 5334 GB we are at the max IOPS

**Provisioned IOPS(IO1/IO2):**

- Critical business application that needs sustained IOPS performance
- Applications that need more than 16000 IOPS
- Great for database workload (Sensitive to storage & consistency)

!!! tip
    Provisioned IOPS (IO1/IO2) **Suppport EBS Multi-Attach**

__IO1 / IO2 (4GiB - 16 TiB)__

    - Max PIOPS: `64,000 for Nitro EC2 instances` & 32,000 for other
    - Can `increase PIOPS independently` from storage size
    - IO2 have more durability & more IOPS per GiB (at the same pricing as IO1)

__IO2 Block Express (4 Gib - 64 TiB)__

    - Sub-millisecond latency
    - Max PIOPS: 256,000 with an IOPS:GiB ratio of 1000:1
    - 260,000 IOPS & 7,500 MB/s per instnace maximum performance

!!! tip "Per Instance Maximum Performance for Provisioned IOPS SSD"

    - IO1 - 260,000 IOPS & throughput of 7500 MB/s
    - IO2 - 160,000 IOPS & throughput of 4,750 MB/s
    - IO2 Block Express - 260,000 IOPS & throughput of 7500 MB/s

**Hard Disk Drives(ST1/SC1):**

    - Cannot be a boot volume
    - 125 GiB - 16 TiB

__Throughput optimized HDD: ST1__
    - Big data, data warehousing, Log Processing
    - Max throughput of 500 MiB  - Max IOPS 500

__Cold HDD: SC1__
    - For data that is infrequently accessed
    - Scanrios where lowest cost is important
    - Max throughput 250 MiB - Max IOPS 250

#### EBS Multi Attach - IO1/IO2

- Attach the same EBS volume to multiple EC2 instances in the same AZ.
- Each instance will have full read/write permission.
- Supports upto **16 EC2 instances** at a time.
- Must use a filesystem that is cluster-aware (not XFS, EX4, etc)

Use case:

    - Acheive higher application availability in clustered linux applications (Eg: Teradata)
    - Application must manage concurrent write operations

#### EBS Encryption

Following is available with EBS volume encryption.

- Data is encrypted inside the EBS volume
- All the data in-flight moving between the instance and the volume is encrypted
- All snapshots are encrypted & all volumes created from snapshots are encrpted.
- Encryption & Decryption is handled transparently. No action required from the user.
- Encryption has minimal impact on latency
- EBS encryption leverages keys from **KMS (AES-256)**
- Copying an unencrypted snapshot allows encryption.
- Snapshot of encrypted volumes are encrytped.
- Any snapshot created forom a non-encrypted volume is not encrypted. But can be encrypted with copy function.
- Each volume uses 1 unique DEK. Snapshots & future volumes created from the snapshot use the same DEK
- Can't change a volume to NOT be encrypted once it is encrypted
- OS is not aware of the encryption. Hence no performance loss

#### EBS Optimized

- Historically network on EC2 was shared between data network & EBS storage network
- EBS Optimized means dedicated capcaity is provided for EBS networking
- Most instances support it & it is enabled by default
- Older instances it is supported but costs extra

### EFS - Elastic File System.

- A managed NFS (Network file system) that can be mounted on many EC2 instances.
- EFS works with ``EC2 instances in Multi-AZ``.
- Highly available & scalable but expensive (3X GP2), pay per use
- Only compatible with Linux based AMIs (Not windows)
- Uses NFSv4.1 protocol
- Uses security group to control access to EFS
- Supports encryption at rest with KMS
- POSIX file system,(~Linux) has a standard file API
- File system scales automatically, no capacity planning

Use case: Content management, web-sharing, data sharing, wordpress

### EFS Performance & Storage class
    - 1000s of concurrent NFS clients, 10 GB +  throughput
    - Grow to PetaByte scale network file system automatically

**Performance mode**: Set at creation time

1. General Purpose: Latency sensitive use cases (WebServer, CMS. etc)
2. Max IO: Higher latency, throughput, highly parellel (big data, media processing)
3. Throughput mode:
    - Bursting: 1 TB = 50 MiB + burst upto 100 MiB
    - Provisioned: Set your throughput regardless of storage size Eg: 1GiB/s for 1 TiB storage

**Storage Classes**:

1. Storage tiers: Lifecycle management feature - Move file after N days
    - Standard: Frequently accessed files
    - Infrequent Access: (EFS-IA): Cost to retrieve files but lower price to store. Can be enabled with a lifecyle policy
2. Availablility & Durability:
    - Regional (Previously:Standard): Multi-AZ, great for Production
    - One Zone: One AZ, great for dev, backup enabled by default, compatible with IFS-IA (EFS One Zone IA). Over 90% cost saving.

### EBS vs EFS

**EBS Volumes:**

    - 1 instance (Except MultiAttach for IO1/IO2)
    - Locked at AZ level
    - GP2: IO increases as the disk size increases
    - IO1: Can increase IO independently
    - To Migrate an EBS volume across AZ (Take a Snapshot & restore the snapshot in another AZ)
    - EBS backup / snapshot use lot of IO & you shouldn't run while application is handling lot of traffic
    - Root EBS volume get terminated by default if EC2 instance is terminated (Can be disabled)

**EFS**

    - Mounting to 100s of instances across AZ
    - Only supports Linux Instances (POSIX)
    - More expensive than EBS
    - Can leverage EFS-IA for cost savings

## EC2 Instance Store

Instance store is physical storage attached to the EC2 instance rather than the network store (EBS volume).

- Better I/O performance / Highest storage performance on AWS
- Physically connected to one EC2 host & instances on that host can acces them
- Instance Store price is included in the instance price (Use it or loose it)
- They must be attached at launch
- EC2 instance store is ephemeral (Dat lost on instance move, resize or hardware failure)
- Good for buffer/cache/temporary data
- Risk of data loss if hardware fails
- Backups & Replication are your responsiblity

## EC2 Instance Metadata

- EC2 service provides data to instances
- Accessible inside all instances
- **Ip Address to access metadata: http://169.254.169.254/latest/meta-data/**
- Common queries: Network, Environment, Authentication, Temporary SSH key (Used while EC2 instance cnnect), User-Data scripts
- Metadata service is not authenticated or encrypted

## Refresher

Cheap = ST1 or SC1
Throughput..Streaming.. ST1
Boot - Not ST1 or SC1
GP2/3 - Upto 16,000 IOPS
IO1/2 - Upto 64,000 IOPS (IO2 block express - 256,000)
RAID0+EBS up to 260,000 IOPS (IO1/2)
More than 260,000 IOPS - instance store (ephemeral)


## EC2 Instance Roles
- Credentials are inside meta-data `iam/security/role-name`
- Credentials are automatically rotated and always valid
- Instance Proiles are a wrapper around IAM Role
- While creating instance role in the console, the instance profile is created automatically (Not created in CLI/Cloudformation)
- Attaching an instance role to the instance through the console translates to attaching the instance profile

```
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

## SSM Parameter Store

- Storage for configuration and secrets
- Ability to store String, StringList & SecureString
- Can store Lincese codes, Database Strings, Configs & Password
- Supports Hierarchies & Versioning
- Supports Plaintext & Ciphertext (User must have permission to interact with KMS)
- Public Parameters - Eg: Latest AMI per region
- Changes can create events that start process in other AWS Products

```
aws ssm get-parameters --names /rate-my-lizard/dbstring
```

```
aws ssm get-parameters-by-path --path /my-cat-app/
```

## Enhanced Networking

- Uses **SR-IOV** - Network Interface Card (NIC) is virtualizationa aware
- NO charge - available on most EC2 types
- Higher IO & Lower host CPU usage
- More bandwidth
- High packets per second (PPS)
- Consistent lower latency