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
- Enable high availavbility across zones
- Seperate public traffic from private traffic.

### Elastic Load Balancer - ELB
A managed load balancer. AWS guarantees uptime, upgrades, maintenance & high availability.
Costs less to than setting up your own load balancer. But, it will be a lot more effort on your end also very difficult to manage from a scalability perspective.

Integrated with many AWS offerings:

    - EC2, EC2 Auto Scaling Groups, Amazon ECS
    - AWS Certificate manager (ACM), cloudwatch
    - Route 53, AWS WAF. AWS Global Accelerator

**Health Checks**

Health check enable the load balancer to know if instances it forwards traffic to are available to reply to requests. Health checks are done on a port & route. (Eg: `/health`)

#### Application Load Balancer (ALB)

Protocols: HTTP, HTTPS, WebSocket

- ALB is layer 7 (HTTP)
- Load balancing to multiple http applications across machines (target Groups)
- Load balancing to multiple applications on the same machine (containers)
- Support `redirect from HTTP to HTTPS`
- Fixed hostname (xxx.region.elb.amazonaws.com)
- The application servers don't see the IP of the client directly. THe IP is inserted in the header `X-Forwarded-For`, port in the `X-Forwarded-Port` & protocol in the `X-Forwarded-Proto`

Supports routing traffic to different target groups:

- Routing based on path in URL: `/users` or `/login`
- Routing based on hostname in URL: `beta.maheshrjl.com` or `maheshrjl.com`
- Routing based on Query String & Headers: `maheshrjl.com/users?id=5&enabled=false`
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
- Rules also will have a Port range & TCP or UDP protocol. For EC2 instances, the source of incoming traffic will be a security group from the load balacner that allows communication on a specific port & protocol.
- `Security group of EC2 instance is linked with security group of LoadBalancer` which means only traffic originating from Load Balancer can communicate with EC2 instance.


#### Network Load Balancer (NLB)

Protocols: TCP, TLS, UDP

- NLB is layer 4
- Allows to forward TCP & UDP traffic to your instances
- Handle millions of traffic per second
- Health Checks support `TCP`, `HTTP` & `HTTPS`
- Less latency **~100 ms vs 400ms** for ALB
- NLB does not have a security group defined
- **Target Groups:**
```
    - EC2 instances
    - Private IP addresses
    - Application Load Balancer (NLB is in front of ALB)
```

!!! note
    NLB has 1 static IP per AZ & supports assignign elastic IP. This is helpful for whitelisting IP. NLb are used for extreme performance, TCP or UDP traffic.


#### Gateway Load Balancer (GWLB)

Protocol: IP

Deploy, scale & manage a fleet of 3rd party network virtual appliances in AWS. Eg Usage: `Firewalls, Intrusion Detection & Prevention, Deep Packet Inspection, Payload manipulation.`

- Operates at Layer 3 (Network Layer)
- Uses **GENEVE Protocol on 6081**
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
    - Can include any custom attributed required by the application
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
- Enabled by default for [ALB](/aws/ha/#application-load-balancer-alb) but disabled by default for [NLB](/aws/ha/#network-load-balancer-nlb) & [GWLB](/aws/ha/#gateway-load-balancer-gwlb)
- No charges for inter AZ data [ALB](/aws/ha/#application-load-balancer-alb) but charges are incurred for [NLB](/aws/ha/#network-load-balancer-nlb) & [GWLB](/aws/ha/#gateway-load-balancer-gwlb)

**Without Cross Zone Load Balacnding**

- Requests are distributed in the instances of the node in the ELB
- Traffic is contained in each AZ

!!! warning
    If there are imbalanced number of EC2 instances, then some instances will receive more traffic when cross zone load balancing is not enabled.

### SSL Certificate

- The load balancer sues an X.509 certificate (SSL/TLS) server certificate
- You can manage certificates using ACM (AWS Certificate Manager)
- You can upload your onwn certificates alternatively

**SSL-SNI**

- Server Name Indication (SNI) solves the problem of multiple SSL certificates onto 1 web server (To serve multiple websites)
- It is a 'newer' protocol, & requires the client to indicate the hostname of the target server in the initial SSL handshake
- Only works for the ALB, NLB & Cloudfront
- `Multiple SSL certificates in Load Balacner -> ALB or NLB`