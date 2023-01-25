---
title: AWS EC2
date: 2023-01-25
---
# AWS EC2 (Elastic Compute Cloud)

## AMI
An Amazon Machine Image (AMI) is a supported and maintained image provided by AWS that provides the information required to launch an instance.

- One or more Amazon Elastic Block Store (Amazon EBS) snapshots, or, for instance-store-backed AMIs, a template for the root volume of the instance (for example, an operating system, an application server, and applications).
- Launch permissions that control which AWS accounts can use the AMI to launch instances.
- A block device mapping that specifies the volumes to attach to the instance when it's launched.


## Security Groups

- Control how traffic is allowed into or out of ec2 instances
- Security groups contain allow rules only
- Rules can be referenced by IP or by security group
- Can be attached to multiple instances
- Locked down to a single region/vpc
- All inbound traffic is blocked & outbound traffic is allowed by default
- They control:
    - Access to Ports
    - Authrozied IPv4 & IPv6 address ranges
    - Control of inbound network (from outside to the instance)
    - Control of outbound network (from the instance to outside)

!!! note

    If the application is not accessible (timeout). Then it is most likely blocked by security group. But, if the
    application throws a `connection refused` then it's an application error or it might not have launched.


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

EC2 user data is used to automate boot tasks such as Installing updates & Installing software. User data runs with root user.

## Purchasing Options

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

Fixed compute, resverved for a certain period of time. Cheaper than on-demand if used for predicatable long term. (1 or 3 years). You reserve a specific instance attribute (Tyoe, Region, Tenancy, OS).
Instances can be reserved in a Zone or AZ. Unused instance can be sold in the marketplace.

Workload:
    - Reserved Instances: long worload
    - Convertible Reserved Intances: long worload with the ability to change instance types

### Savings Plan

Commitment to an amount of usage for 1 or 3 years. Suitable for long workloads. Commit to a certain type of usage.
Eg: (5$ per hour for 1 or 3 years.) Any usage beyond saving plan is billed On-Demand. Also, you're locked to a specific instance family & Region.

### Dedicated Host

Book an entire physical server. Physical EC2 server are available only to customer. Used when regulatory requirements specify that you must not be using multi-tenant computing.

- Allows customer to address compliance requirements, use existing server-bound software licenses & control instance placement.
- Dedicated hosts can be purchased on-demand or reserved for 1/3 years
- Most expensive option
- Customer gets access to the physical server & visibility into lower level hardware

### Dedicated Instances

EC2 instances running on hardware dedicated to a single customer. Hardware can be shared with other instances in the same account & there is no control over instance placement.

- Own instance on your own hardware

## Capacity Reservation

Planning in advance for capacity

- Reserve On-Demand instances capacity in a specific AZ for any duration
- No time commitments (Create/Cancel anytie) also, no billing discounts are available
- Can be combined with regional reserved instances & savings plan for cost saving
- Charged on-demand rates whether or not you run instances

Workload:
    - Suitable for short term uninterrupted worloads in a specific AZ