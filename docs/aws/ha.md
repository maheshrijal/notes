---
title: High Availability & Scalability
date: 2023-01-24
---
# High Availability & Scalability in AWS


## Scalability

Scalability means that an application / system can handle greater workloads by adapting. There are 2 types of scalability:

1. Horizontal Scalability (Elasticity) - Provision additional servers quickly based on demand
2. Vertical Scalability - Increase size / resource of exisitng server

## High Availability

High Availability means running applications / system in multiple AZ's to be able to survive in case of a data center wide outage. HA can be passive (Eg: RDS Multi AZ) or active (Eg: Horizontal scaling).

## Load Balancer

Load balancers are servers that forward traffic to multiple servers / EC2 instances downstream. Load balancers are useful for following use cases:

- Spread load across multiple downstream instances
- Expose a single point of access (DNS) to your application
- Seamlessly handle failure of downstream instances
- Regular health check & SSL termination for websites.
- Enforce stickiness with cookies
- Enable high availability across zones
- Seperate public traffic from private traffic.
- Load balancer require 8+ free IPs per subnet and `/27` subnet to allow scaling.

!!! Note

    EC2 instance does not need to be public to work with LoadBalancer. `/28` is the minimum subnet required for Load Balancer.

### Elastic Load Balancer - ELB
A managed load balancer. AWS guarantees uptime, upgrades, maintenance & high availability.
Costs less to than setting up your own load balancer. But, it will be a lot more effort on your end also very difficult to manage from a scalability perspective.

Integrated with many AWS offerings:

    - EC2, EC2 Auto Scaling Groups, Amazon ECS
    - AWS Certificate manager (ACM), cloudwatch
    - Route 53, AWS WAF, AWS Global Accelerator

**Health Checks**

Health check enable the load balancer to know if instances it forwards traffic to are available to reply to requests. Health checks are done on a port & route. (Eg: `/health`)

#### Application Load Balancer (ALB)

Protocols: HTTP, HTTPS, WebSocket

- ALB is layer 7 (HTTP/HTTPS)
- Load balancing to multiple http applications across machines (target Groups)
- Load balancing to multiple applications on the same machine (containers)
- Support `redirect from HTTP to HTTPS`
- Fixed hostname (xxx.region.elb.amazonaws.com)
- The application servers don't see the IP of the client directly. THe IP is inserted in the header `X-Forwarded-For`, port in the `X-Forwarded-Port` & protocol in the `X-Forwarded-Proto`
- Does not support other layer 7 protocols (SMTP, SSH, Gaming) and no TCP/UDP/TLS listeners
- HTTP HTTPS (SSL/TLS) always terminated on the ALB - no unbroken SSL between LoadBalancer & Application. (New SSL connection initiated from LB to application)
- ALB are slower than NLB

Supports routing traffic to different target groups:

- Routing based on path in URL: `/users` or `/login`
- Routing based on hostname in URL: `beta.maheshrijal.com` or `maheshrijal.com`
- Routing based on Query String & Headers: `maheshrijal.com/users?id=5&enabled=false`
- **Target Groups:**
```
    - EC2 instances (Can be managed by Auto Scaling Group)
    - ECS tasks (managed by ECS)
    - Lambda Functions (HTTP requests translated into a JSON event)
    - Private IP Addresses
    - ALB can route to multiple target groups
    - Health checks are at the target group level
```

ALB are a great fit for micro services & container based application. Also, supports port mapping for dynamic ECS applications.

**Load Balancer Security Groups**

- Load Balancer security groups will have source as `0.0.0.0/0` so that users can access from anywhere.
- Rules also will have a Port range & TCP or UDP protocol. For EC2 instances, the source of incoming traffic will be a security group from the load balancer that allows communication on a specific port & protocol.
- `Security group of EC2 instance is linked with security group of LoadBalancer` which means only traffic originating from Load Balancer can communicate with EC2 instance.


#### Network Load Balancer (NLB)

Protocols: TCP, TLS, UDP

- NLB is layer 4 (Transport)
- No visibility/understanding of HTTP or HTTPS
- No header, no cookie, no session stickiness
- Allows to forward TCP & UDP traffic to your instances
- Handle millions of traffic per second
- Health checkjust chec ICMP/TCP Handshake. Not application aware.
- Less latency **~100 ms vs 400ms** for ALB
- NLBs can have static IP's. Useful for whitelisting
- Can forward TCP traffic straight to instances without breaking encryption
- Used with private link to provide services to other VPCs
- NLB does not have a security group defined
- **Target Groups:**
```
    - EC2 instances
    - Private IP addresses
    - Application Load Balancer (NLB is in front of ALB)
```

!!! note
    NLB has 1 static IP per AZ & supports assigning elastic IP. This is helpful for whitelisting IP. NLb are used for extreme performance, TCP or UDP traffic.


#### Gateway Load Balancer (GWLB)

Protocol: IP

Deploy, scale & manage a fleet of 3rd party appliances in AWS. Eg Usage: `Firewalls, Intrusion Detection & Prevention, Deep Packet Inspection, Payload manipulation.`

- Operates at Layer 3 (Network Layer)
- Inbound and Outbound traffic (transaparent inspection and protection)
- Uses **GENEVE Protocol on 6081**
- GWLB endpoints --> (Traffic enters/leaves via these endpoints)
- Combines the following functions:
    - **Transparent Network Gateway:** Single entry/exit for network traffic
    - **LoadBalancer:** Distributes traffic to virtual appliances


### Session Affinity

Implement `stickiness` so that the same client is always redirected to the same instance / container behind a load balancer.

- Works for Application Load Balancer (ALB)
- Uses a `cookie` which has an expiration date that you can control.
- Use case: Make sure the user doesn't loose their session data.

**Types of Cookies**

1. Application based cookie

    **Custom Cookie**

    - Generated by target (application)
    - Can include any custom attributes required by the application
    - Cookie name must be specified individually for each target group
    - Reserved cookie names: `AWSALB`, `AWSALBAPP` or `AWSALBTG`

    **Application cookie**

    - Generated by the load balancer
    - Cookie name is `AWSALBAPP`

2. Duration based Cookie

    - Cookie generated by the load balancer
    - Cookie name is `AWSALB` for ALB
    - Duration is generated by the load balacner


!!! warning
    Enabling stickiness can imbalance to the load over the backend EC2 instances.

### Cross Zone Load Balancing

**With Cross Zone Load Balancing**

- Each load balancer distributes evenly across all registered instances in all az.
- Enabled by default for [ALB](ha.md#application-load-balancer-alb) but disabled by default for [NLB](ha.md#network-load-balancer-nlb) & [GWLB](ha.md#gateway-load-balancer-gwlb)
- No charges for inter AZ data for [ALB](ha.md#application-load-balancer-alb) but charges are incurred for [NLB](ha.md#network-load-balancer-nlb) & [GWLB](ha.md#gateway-load-balancer-gwlb)

**Without Cross Zone Load Balacnding**

- Requests are distributed in the instances of the node in the ELB
- Traffic is contained in each AZ

!!! warning
    If there are imbalanced number of EC2 instances, then some instances will receive more traffic when cross zone load balancing is not enabled.

### Deregistration Delay

- Time to complete 'in-flight requests' while the instance is de-registering or unhealthy
- Stops sending new requests to the EC2 instance which is de-registering
- Between 1 - 3600 seconds (Default: 300 seconds)
- Can be disabled (Set to 0)
- Set to low value if requests are short

### SSL Certificate

- The load balancer issues an X.509 certificate (SSL/TLS) server certificate
- You can manage certificates using ACM (AWS Certificate Manager)
- You can upload your onwn certificates alternatively

**SSL-SNI**

- Server Name Indication (SNI) solves the problem of multiple SSL certificates onto 1 web server (To serve multiple websites)
- It is a 'newer' protocol, & requires the client to indicate the hostname of the target server in the initial SSL handshake
- Only works for the ALB, NLB & Cloudfront
- `Multiple SSL certificates in Load Balancer -> ALB or NLB`


## Launch Configuration (LC) & Launch Templates (LT)

Allow you to definethe configuration (AMI, Instance type, Strage, Key Pair, Networking, Security Groups, Userdata & Iam Role) of an EC2 instance in advance.

- Once configured not editable.However, Launch Templates is a newer featrue & has versions.
- LT provide newer features including T2/T3 unlimited CPU option, place groups,capacity reservations, elastic graphics
- LC & LT are used as part of Auto Scaling Groups. But, LT can be used to save time when provisioning EC2 instances from the console/CLI

## Auto Scaling Group

The role of auto scaling group (ASG) is to:

- Scale out (Add EC2 instances) to match an increased load
- Scale in (Remove EC2 instances) to match decreased load
- Ensure we have minimum & maximum number of EC2 instances running
- Automatically register new instances to a load balacner
- Re-create EC2 instance in case previous one is terminated or unhealthy
- It is possible to scale ASG based on cloudwatch alarms

!!! note
    ASGs are free (You only pay for underlying EC2 instances)

ASG are created with a **Launch Template**. It Contains information about how to launch EC2 instances

- AMI + instance type
- EC2 user data
- SSH Keys
- EBS Volumes
- IAM Roles
- Network + Subnet Information
- Load balancer information
- ASG has a min size, max size & scaling policies

### Scaling Policies

1. Manual Sacling
    - Manually adjust the desired capacity

2. Scheduled Scaling
    - Anticipate scaling based on known usage patterns
    - Eg: increase min capacity to 10 at 6 PM on Friday

#### Dynamic Scaling

1. Simple scaling
    - When cloudwatch alarm is triggered Eg( CPU > 80%) add 2 units
    - Eg: "If CPU utilization above 50 % add +1 to desired if the below 50% remove 1 from desired."

2. Step Scaling
    - Similar to simple scaling but contains more detailed rules.
    - React quicker to extreme change in conditions.
    - Great for variable load.
    - Eg: At 1 instance the CPU utilization is 50% & there is sudden spike in load add +3 instances.

3. Target Tracking
    - Target the desired aggregate utilization (CPU, network in/out, request count per target for ALB)
    - Eg: Track the average ASG CPU to stay at a percentage (40%)

4. Scaling based on SQS - ApproximateNumberOfMessagesVisible

!!! warning
    ASGs have a Cooldown Period configuration.It controls how long to wait at the end of a scaling action before starting another action. After a scaling activity happens, you are in the **cooldown period (Default 300 seconds)**. During the cooldown period ASG will allow for metrics to stabilize.
    Use ready-to-use AMI to reduce configuration time in order to be serving requests faster & reduce cooldown period.

### ASG Health Checks

**EC2**: Instance is marked unhealthy if any of these statuses is marked as unhealthy (Stopping, Stopped, Terminated, Shutting Down or Impaired)

**ELB**: Instance should be running & passing the ELB health check
    - Checks can be application aware for ALB (Layer 7)

**Custom**: Instances marked healthy/unhealthy by an external system

!!! info

    Health check grace period (Default 300 s)- delay before starting health checks

### SSL Offload

**Bridging**

- The default mode for ALB
- One or more clients makes one or more connections to a LoadBalancer
- Listener is configured for HTTPS
- SSL Connection is terminted on the ELB & the ELB needs same certificate for the domain name as the aplication
- ELB initates a new SSL connection to backend instances
- Backend instances need SSL certificates and the compute required for cryptographic operations

**Pass-through** (NLB)

- The client connected to LoadBalancer and the LoadBalancer passes the connection to the backend instances.
- Connection encryption is maintained between client and the backend instances
- Instances still need the SSL certificates installed, but the LoadBalacner does not need it
- Listener is configured for TCP.

**Offload**

- Clients connect to the LoadBalancer with & the connection is terminted on the LoadBalancer
- Listener is configured for HTTPS
- SSL Connection is terminted on the ELB and the ELB initiates connection to the backend instances using HTTP
- No certifcates or cryptographic requirement for backend instances

### Connection Stickiness

- Stickiness generates a cookie `AWSALB` which locks the device to a single backend instances for a duration (1 second to 7 days)
- Change to session stickiness will occur when either the instances fails or the cookie expires