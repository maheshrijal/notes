# AWS Virtual Private Cloud

A VPC is a virtual network inside AWS. A VPC is within 1 account & 1 Region.

- VPC is Private & Isolated unless you decide otherwise.
- Services deployed in the same VPC can communicate, but outside connections are blocked by default.
- Default VPC (maximum 1 per region) and custom VPC are available.
- Default VPC is public by default and is less flexible than custom VPC.
- Default VPC CIDR: `172.31.0.0/26`
- A VPC needs to have /28 at minimum (16 IPs) & /16 (65536 range)
- VPC can be Default or Dedicated Tenancy
- Optional single assigned IPv6 /56 CIDR block (All IPv6 are publicly routed by default. But traffic needs to be explcitily allowed)
- VPC have fully featured DNS provided b Route 53.  Available VPC DNS: `Base IP + 2` address.
- enableDNSHostnames - assigns public DNS hostnames to instances
- enableDNSSupport - enables DNS resolution in VPC

## VPC Subnets

- Subnet is an AZ resilient service within a particular AZ.
- One subnet can be created in a specific AZ in a region. It cannot be in more than 1 AZ. (One AZ can have many subnet)
- IPv4 CIDR in a VPC cannot overlap with other subnets
- Subnets can optionally be allocated IPv6 CIDR `/64` as long as the VPC is enabled for IPv6
- By defualt subnets in a VPC can communicate with other subnets in the VPC.
- IP Allocation options in subnet: `Auto Assign Public IPv4` & `Auto Assign IPv6`

## Subnet IP Addressing

**Reserved IP Address (5 total)**

Eg: 10.16.16.0/20 (10.16.16.0 -> 10.16.31.255)

- Network (First IP address): `10.16.16.0` (Network Address)
- `Network + 1` (Second IP address): `10.16.16.1` (Used by VPC Router)
- `Network + 2` (Third IP address): `10.16.16.2` (Reserved for DNS)
- `Network + 3` (Fourth IP address): `10.16.16.3` (Reserved for future use)
- Broadcast (Last IP): `10.16.31.255` (Broadcast address is reserved even though broadcast is not supported in VPC)

!!! tip

    A VPC configuration object **DHCP Options Set** is assigned to a VPC. One option set is applied at one time & the configuration flows to subnets as well. It can be changed, but cannot be edited.

## VPC Router

- Every VPC has a VPC Router - Highly available
- IN every subnet `network +1 ` address is used by the VPC router
- Routes traffic between subnets
- Controlled by `route tables` in each subnet
- A VPC has a main route table - subnet default (A subnet can have only 1 route table associated at any time but a route table can be associated with many subnets)
- In the route table, higher prefix means more priority. But, this does not apply to local routes they always take priority.
- Target `local` in the route table means the destination is local to the VPC.

## Internet Gateway

- Regionally resilient gateway attached to VPC (1 IGW will cover all AZs in the region.)
- A VPC can have no internet gateway (Private VPC) or 1 internet gateway. However, IGW can have 0 attachments but it can only be attached to 1 VPC at a time.
- Runs within the AWS public Zone
- Gateways traffic between the VPC & the internet or AWS Public Zone (S3, SNS, SQS etc)
- A managed service (AWS handles performance)

!!! danger "IPv4 address with IGW"

    Public IPv4 addresses are not attached to the EC2 OS, but a record is maintained in the IGW mapping the private IPv4 to public IPv4. The OS is not aware of the public IPv4 address at any point. In IPv6, the address is directly assigned to OS.


## Network Access Control Lists (NACL)

- NACLs are associated with subnets. Every subnet has an associated NACL.
- NACL only impacts data crossing the subnet boundry.
- NACLs are stateless. Rules are required for both inbound & outbound connections.
- NACL rules match destination IP/IP Range, destination PORT/PORT range along with Protocol & they offer explicit **ALLOW** or **DENY**
- Rules are matched in order, lowest rule number first & once a rule is matched, processing stops.
- By Default all traffic is allowed.
- Each subnet can have ony NACL (Default or Custom)
- A NACL can be associated with many subnets
- Custom NACLs can be created fir a specific VPC and are initially associated with no subnets. They have 1 inbound & outbound rule with default deny.

## Security Groups (SG)

- Security Groups are stateful - detect response traffic automatically
- No explicit deny - Only allow or implicit deny
- Support IP/CIDR based rules along with AWS logical resources including other security groups & itself
- **SGs are attached to ENIs** & not instances (UI might show as being attached to EC2 instances)
- SGs are locked down to a single region/vpc
- All inbound traffic is blocked & outbound traffic is allowed by default

!!! tip

    If the application is not accessible (timeout). Then it is most likely blocked by security group. But, if the application throws a connection refused then it's an application error or it might not have launched.

## NAT Gateway

- Runs from a public subnet (To deploy a NAT Gateway a VPC Should have public subnets,  an internet gateway, subnets configured to allocated public IPv4 address,default reoutes for the subnets pointing to internet gateway.)
- AZ resilient service (HA within the AZ)
- For region resilience, a NATGW in each region is required & the route table in each AZ should use that NATGW as target
- A managed service, scales to 45 Gbps, billed for duration & data volume
- NAT Gateway do not support port forwarding & cannot be used as bastion host.
- NAT Gateway cannt use Security Group
- NAT Gateways don't work on IPv6

!!! tip

    For IPv6 connectivity we can add a `::/0` & point it to internet gateway for bi-directional connectivity.

## VPC Flow Logs

- Flow logs only capture packet metadata not packet contents
- Flow logs work by attaching virtual monitors to
    1. Attached to VPC - All ENIs in that VPC
    2. Subnet - All ENI in that subnet
    3. ENIs directly
- Flow logs are **not realtime**
- Log destinations: S3 (Query S3 logs using Athena) or CloudWatch Logs
- ICMP: 1, TCP: 6, UDP: 17
- Traffic that is not logged: Metadata service (192.254.169.254, 192.254.169.123), DHCP, Amazon DNS Server, Amazon Windows License Server

## Egress-Only Internet Gateway

- NAT gatway allowes private IPv4 addresses to access public networks without allowing externally initated connections
- With IPv6 all IPs are public therefore Internet Gateway would allow all IPv6 connections IN and OUT
- **Egress-Only igw is boutbound-only for IPv6** networks
- HA by default across all AZs in the region
- Default IPv6 Route of `::/0` should be added to route table with egress only internet gateway as target

## VPC Endpoints

### Gateway Endpoint

- Provide private access to S3 & DynamoDB
- Normally we would need internet gateway to access S3 & DynamoDB since these services reside in AWS public zone
- Prefix List is added to route table as a target to Gateway Endpoint
- **HA across AZs in a region** by default
- Gateway endpoints are only **accessible within the VPC**
- Endpoint policy is used to control what it can access (Eg: specific S3 buckets)
- Regional service (Can't access cross-region services)

Use cases:

1. Private VPC which needs access to public resources (S3/DynamoDB)
2. Prevent Leaky Buckets - S3 buckets can be set to private only by allowing access only from gateway endpoint

### Interface Endpoint

- Provide private access to public AWS services
- Interface endpoints started supporting S3 recently, but DynamoDB is still supported only through Gateway Endpoint
- Interface Endpoints are added to specific subnets in a VPC - ENI and are **not HA** by default.'
- 1 interface endpoint is deployed to 1 AZ and there is no resilience in case of failures
- For HA, one interface endpoint is added to one subnet per AZ in the VPC
- Network access is controlled via Security Groups
- Endpoint Policies: restrict what can be done with the endpoint
- Support **TCP & IPv4 only**
- Uses **PrivateLink** in the backend
- Endpoint provides multiple DNS endpoints (DNS name will resolve to private IP address of the services)
    1. Regional DNS name
    2. Zonal DNS name
- Applications can either use the above 2 DNS names or use **PrivateDNS(associate route 53 private hosted zone with your VPC)** which overrides the default DNS for services which means applications can use interface endpoitns without any modification. This is now enabled by default

## VPC Peering

- Direct encrypted network link between **two VPCs only**
- Works same/cross-region and same/cross-account
- Public hostnames of services in the peered VPC resolve to private IPs (Optional)
- **Same region SG's** can reference peered VPC SG Ids
- VPC Peering is not transitive
- Routing configuration is needed but, traffic should be allowed in SGs & NACLs
- VPC Peering connections cannot be created where there is overlap in the VPC CIDRs