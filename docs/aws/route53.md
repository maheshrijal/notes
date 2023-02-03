---
title: AWS Route 53 in AWS
date: 2023-02-03
---

# Route 53


## CNAME vs Alias

**CNAME**

- Points to a hostname (app.domain.com -> bla.example.com)
- Only for Non Root domain (xyz.example.com)

**Alias**

- Points to hostname to an AWS Resource (app.domain.com -> rds.amazonaws.com)
- Work for root domain & Non Root domains
- Alias records are free
- Native health check
- Unlike CNMAE it can be used for Zone Apex (Eg: example.com)
- Alias records if always of type A/AAAA for AWS resource
- TTL cannot be set for Alias records

Target: ELB, Cloudfront Distribution, API Gateway, Elastic Beanstalk, S3 Websites, VPC Interface Endpoints, AWS Global Aceelerator, Route 53 Records in the same hosted zone

!!! warning
    Alias records cannot be set for a EC2 DNS name

## Routing Policy

### Simple Routing Policy

- Typically router traffic to a single resource
- If you have multiple resources with the same name, simple routing policy will route traffic to one of the resources at random
- If alias records are used, you can specify only one AWS resource
- **Cannot associate health checks** with simple routing policy

### Weighted Routing Policy

- Route traffic to multiple resources based on a percentage
- Each record set can have a weight associated with it
- Can be associated with health checks
- DNS records must have same name and type
- Use case: A/B testing, Blue/Green deployment, Load balancing across multiple AWS regions
- A weight of 0 means no traffic will be routed to the resource, if all records are set to 0, all records will be returned equally

### Latency Based Routing Policy

- Route traffic to resources based on the lowest latency
- Latency is based on traffic from the end user to the AWS region
- Can be associated with health checks

### Failover Routing Policy

- Route traffic to resources based on the health of the primary resource
- If the primary resource is healthy, Route 53 will route traffic to the primary resource otherwise it will route traffic to the secondary resource
- Use case: Primary resource is in one AZ, secondary resource is in another AZ
- Can be associated with health checks

### Geolocation Routing Policy

- Route traffic to resources based on the location(Continent/Country/US State) of the end user
- Must create a default record in case there is no match on the user location
- Can be associated with health checks
- Use case: Localization, Restrict content distribution


### Geoproximity Routing Policy

- Route traffic to resources based on the location of the end user and the location of AWS resources
- Must create a default record in case there is no match on the user location
- Can be associated with health checks
- Ability to **shift traffic from one region to other** based on the defined bias
- Expand Traffic (Bias: 1 - 99) Decrease Traffic (Bias: -1 - -99)
- Resource can be AWS Resources or On-Premise resources (Specify Latitute/Longitude)
- Must use Route 53 traffic flow(advances) to use this feature

### Multivalue Answer Routing Policy

- Route traffic to multiple resources
- Route 53 will return multiple IP addresses for the same record
- Route 53 will return up to 8 IP addresses
- Can be associated with health checks
- MultiValue is not a replacement for ELB

## Health Checks

- Route 53 health checks are used to monitor the health of your resources
- HTTP/HTTPS health checks are free & available for public resources

Types of Health Checks

**Health checks that monitor an endpoint**

- 15 global health checkers will check the endpoint every 30 seconds (Can be changed to 10 seconds - Higher cost)
- If over 18% of health checks pass Route 53 considers the resource healthy
- Protocol: HTTP/HTTPS & TCP
- Health checks will pass if the endpoint returns a 2xx or 3xx response
- For text based responses AWS will check for the string in the first 5120 bytes of the response body
- Router/Firewall/Security Group must allow traffic from Route 53 Health Chekers IP address range to the resource for health checks to work


**Health checks that monitor other health checks (Calculated health checks)**

- Combine the health of multiple health checks to determine the health of a resource
- Conditions: AND, OR, NOT can be used
- Can monitor up to 256 health checks
- Can specify a custom health threshold for passing health checks

**Health checks that monitor CloudWatch alarms (Private Resources)**

- Route 53 health checks can monitor CloudWatch alarms allowing you to monitor the __health of private endpoints__
- You can create a cloudwatch metric & associate it with a CloudWatch alarm then, create a Health Check that monitors the CloudWatch alarm