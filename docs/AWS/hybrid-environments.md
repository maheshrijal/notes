---
title: Hybrid environments and migration
date: 2024-03-27
---

# Hybrid environments and migration

## AWS Site to Site VPN

- A logical connection between a VPC and on-premises network encrypted using IPSec, running over public internet
- Full HA if designed and implemented correctly
- Quick to provision (Can be provisioned in less than an hour)
- VPN can be used as a backup for Direct Connect (DX)
- Components required to provision:

    - VPC
    - Virtual Private Gateway (VGW)
    - Customer Gateway (CGW) - Either logical component in AWS or physical device in on-premise
    - VPN Connection between VGW and CGW

!!! warning "VPN Considerations"

    - There is a speed limit of ~ **1.25 Gbps** on the AWS Side for VPN
    - Cap for all VPNs connecting to a  Virtual Private Gateway is also ~ 1.25 Gbps
    - Latency consideration - inconsistent and connection transits over public internet

Cost: AWS hourly cost, GB out cost & also data cap for on premise networks

Speed of setup: Can be setup in hours because it's all software configuration

!!! note "DYNAMIC VPN"

    For dynamic VPNs router must support BGP

## Direct Connext (DX)

- A physical connection (1, 10 or 100 Gbps)
- Connection is between a Business premises -> DX location -> AWS Region
- For DX, AWS provides a port allocation at a DX location
- Port has an Hourly Cost & Outbound data transfer
- AWS will take time to allocate port & once allocated customer needs to arrange connection to the DX location
- DX provides low consistent latency & high speeds
- Can access AWS Private services (VPCs) and AWS Public Services. It cannot access the internet unless a proxy or other networking appliance is added

### Direct Connect + Public VIF + VPN

- VPN gives encrypted & authenticated tunnel
- Running a VPN over DX provides low & consistent latency
- Public VIFs+IPSec VPN is a way to provide access to private VPC resources, using an encrypted IPSEC tunnel for transit.

## Transit Gateway

- Transit Gateway is Network Transit Hub to connect VPCs to on premises networks
- Significantly reduces network complexity
- Single network object, but is HA and scalable
- Attachments to other network types
- Valid attachments: VPC, Site to Site VPN, Direct Connect Gateway
- Supports transitive routing
- Can be used to create global networks
- Share between account using AWS RAM (Resource access manager)
- Peer with different regions same or cross account
- Less complexity

## Storage Gateway

- Usually runs as a VM on premise or can be ordered as a hardware appliance
- Acts as a bridge between storage that exists on-premise & AWS
- Presents storage using iSCSI, NFS or SMB
- Integrates with EBS, S3 and glacier
- Use case: Migrations, Extension of datacenter into AWS, Storage Tiering, DR and replacement of backup systems

### Storage Gateway - Volume Stored

- All data is stored locally (on-premises)
- Great for `full disk` backups of servers
- Low latency access to data
- Assist with disaster recovery because the snapshots can be used to create EBS volumes
- Doesn't improve datacenter capacity. Main copy of data is stored on -premises
- Use case: Full disk backups & disaster recovery
- 32 Volumes per gateway, 16TB per volume and 512 TB per gateway

### Storage Gateway - Volume Cached

- The primary location for the data is (S3 in AWS) -- Managed by AWS
- Data stored in AWS but Cached on-premises
- Capacity Extension: Allows for data center extension because only cached data is required to be stored on premise
- 32 volumes per gateway, 32 TB per volume, 1PB per gateway

### Storage Gateway - Tape - VTL Mode

- Large backups that backup to TAPE
- Pretends to be an iSCSI tape library, changer and drive
- LTO-9 media can hold 24TB raw uncompressed data & with compressions store upto 60TB
- Provide **sequential** access, Random access is not possible (Eg: SSD, Disk)
- Data modification not possible, data must be overwritten
- Tape has a upload buffer and local cache, it uses to store actively used data
- On prem storage gateway communicates to **Virtual Tape Library (S3)** or **Virtual Tape Shelf (Glacier)**
- It caches stuff locally & uploads in background to virtual tape library
- Virtual Tape size 100 GiB -> 5 TiB (also max size for S3 object)
- Can store 1 PB across 1500 virtual tapes
- When virtual tape is exported, it archives data into VTS which is Glacier or Glacier Deep Archive (Unlimited storage)

### Storage Gateway - File Mode

- Bridges on-premises file storage and S3
- Mount point (shares) available via NFS or SMB
- Files stored into a mount point, are visible as objects in an S3 bucket
- Read and Write Caching ensure LAN-like performance
- Mapping between filename and object name in S3. Structure is also preserved
- A bucket share = AWS S3 bucket & On-Premises file share
- 10 buckets shares per file gateway



## Snow Family

### Snowball

- A physical device ordered from AWS where you log a job and AWS delivers the device (Not instant)
- Data stored in snowball is encrypted with KMS
- 50TB or 80TB capacity
- Connect to the device with 1 Gbps or 10 Gbps ethernet
- Economical range: 10TB to 10PB of data (Can order multiple devices & can be sent to multiple business premises)
- Includes only storage and no compute

### Snowball Edge

- Provides both Storage and Compute
- Larger capacity vs Snowball
- 10 Gbps RJ45, 10/25 SFP, 45/50/100 Gbps (QSFP+)
- Storage Optimized (with EC2) - 80TB, 24 vCPU, 32 GiB RAM or 1TB SSD with EC2 option
- Compute Optimized 100TB + 7.68 GB of NVME, 52 vCPU and 208 GiB RAM
- Compute with GPU - same as above, but with a GPU
- Ideal for remote sites or where data processing on ingestion is required

### Snowmobile

- Portable DC within a shipping container on a truck
- Special order
- Ideal for single location when 10 PB+ required
- Up to 100 PB per snowmobile
- Not economical for multi-site (unless huge) or sub 10 PB

## AWS Directory Service

- Directory stores objects (users, groups, computers, server, file shares) with a structure (domain/tree)
- Multiple trees can be grouped into a forest
- Open Source example of Active Directory Domain Services (AD DS) -> SAMBA (partial compatibility with active directory)

- AWS Managed implementation of directory service
- Runs within a VPC (Private Service)
- To implement HA, deploy into multiple AZs
- Some AWS services need a directory eg: Amazon Workspaces
- Can be isolated (Inside AWS only)
- Can be integrated with existing on-premises
- Can act as `proxy` back to on-premises directory

### Simple AD Mode

- Standalone directory which uses SAMBA 4
- Up to 500 users (small) or 5000 users (large)
- Integrates with AWS Services - EC2instance can join simpleAD and workspaces can use it for logins and management
- Not designed to integrate with any existing on-premises directory system such as Microsoft AD

### AWS  Managed Microsoft AD

- Primary running location is in AWS. Trust relationships can be created b/w AWS & on premises directory
- Connection from AWS to on-prem can happen only via private networking (Direct Connect / Site to Site VPN)
- Resilient if the VPN/Direct Connect fails. Services in AWS will still be able to access the local directory
- Full Microsoft AD DS running in 2012 R2 Mode
- Supports microsoft AD aware applications running in AWS

### AD Connector

- Establish private network connectivity b/w AWS and on-premise network
- Create AD connector and point to on-prem
- It is a proxy, that exists to integrate with AWS services running in cloud (EG: Workspaces). Proxies all requests to on-premises
- Allows AWS services which need a directory to use an existing on-premises directory

## AWS DataSync

- Data transfer service TO and FROM AWS
- Migrations, Data Processing Transfers, Archival/Cost Effective Storage or DR
- Designed to work at huge scale (10Gbps per agent ~ 100 TB per day)
- Keeps metadata (Eg: permissions/timestamps)
- Built in data validation
- Use bandwidth limiters to avoid link saturation
- Support incremental and scheduled transfer options
- Supports compression and encryption
- Automatic recovery from transit errors
- AWS Service integration: S3, EFS, FSx
- Pay as you use. Per GB cost for data moved

!!! note

    The DataSync agent runs on a Virtualization platform such as VMWare and communicates with the AWS DataSync Endpoint


**Task** : A `job` within DataSync defines what is being synced, how quickly, FROM where to WHERE

**Agent**: Software used to read or write to on-premises data stores using NFS or SMB

**Location**: Every task has two locations. FROM and To. Eg: NFS, SMB, Amazon EFS, Amazon FSx, and Amazon S3

## FSx for Windows File Server

- Fully managed **native windows file servers/shares**
- Designed for integrating with windows environments
- Integrates with Directory Service or Self-Managed AD (On-premises)
- Single or Multi-AZ within a VPC
- On-demand and Scheduled backups
- Accessible using VPC, Peering connections, VPN, Direct Connect
- Native windows file system with support for de-duplication, Distributed file system (DFS), KMS at rest encryption and enforced encryption in transit
- Accessed via SMB
- Support for volume shadow copies (file level versioning)
- Keywords/Features for FSx

    - VSS - User Driven Restores
    - Native file system accessible over SMB
    - Windows permission model
    - Supports DFS (Distributed file system) - scale out file share structure
    - Managed - no file server administration
    - Integrates with Directory Service and your own Active Directory

## FSx for Lustre

- Managed Lustre - Designed for **HPC - LINUX** clients and **POSIX** style permissions
- Machine learning, Big Data, Financial modelling,  SageMaker
- 100's GB/s throughput & sub millisecond latency
- Accessible over VPN or Direct Connect
- Deployment types:

**Scratch** - Highly optimized for Short term, no replication & fast. NO HA, NO Replication, & for short term or temporary workloads

**Persistent** - longer term, HA(in one AZ), self healing

!!! tip

    A file system is associated with a repository (Eg: S3) when created. Initially the data is visible in the file system but not actually stored there. Data is **Lazy Loaded** from S3 into the file system as it's needed(on access).
    Data can be exported back to S3 at any point using the **hsm_archive** command


## AWS Transfer Family

- Managed file transfer service - Supports transferring to or from S3 and EFS
- Provides managed **servers** which support protocols
- **FTP** - Unencrypted file transfer
- **FTPS** - (FTP Secure) File transfer with TLS encryption
- **SFTP** - File transfer over SSH
- **AS2** - (Applicability Statement 2) Structured B2B data
- Identities - Service managed, Directory Service, Custom (Lambda/APIGW)
- Managed File Transfer Workflow (**MFTW**) - serverless file workflow engine
- MultiAZ - resilient and scalable
- Charges based on provisioned server per hour + data transferred
- With FTP and FTPS only Directory Service or Custom IDP (Identity Providers) are supported

- **FTP - VPC only** (Cannot be public)

- **AS2 - VPC Internet/internal only**

### Endpoint Types

**Public**

- Endpoint running in AWS public Zone
- Only supports SFTP
- Has a dynamic IP which can change
- Cannot use network access controls or security groups

**VPC - Internet**

- Run inside a VPC
- Can use SFTP, FTPS, AS2
- Accessible via DX/VPN
- Can use SG and NACL
- Static Private IP
- Allocated with **elastic IP** (Accessible over public internet)

**VPC - Internal**

- Run inside a VPC
- Can use SFTP, **FTP**, FTPS, AS2
- Accessible via DX/VPN
- Can use SG and NACL
- Static Private IP
