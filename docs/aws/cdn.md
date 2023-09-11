---
title: Cloudfront & Global Accelerator
date: 2023-03-07
---

## Cloudfront

Cloudfront is **CDN**, it improves application performance by caching content in edge locations. It consists of.

!!! info "DDOS Protection"
    Cloudfront offers DDOS protection as it is worldwide service & is integrated with Shielf, AWS WAF

### Origins

These can be the source of data/origin for cloudfront content.

**S3 bucket**

    - Distribute files by caching them at the edge.
    - Enhanced security with Cloudfront Origin Access Control (OAC)
    - CloudFront can be used as in ingress (Upload data to S3)

**Custom Origin**

    - ALB
    - EC2 instance
    - S3 website (must enable static website on S3 bucket)
    - Any HTTP backend

### Cloudfront vs S3 CRR

| Cloudfront                                                 | S3 Cross Region Replication (CRR)                                                    |
| ---------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| Global edge network                                        | Must be setup for each region where replication is required                          |
| Files are cached for a TTL                                 | Files are updated in near real-time                                                  |
| Can be used to upload data to S3                           | Read only                                                                            |
| Great for static content that must be available everywhere | Great for dynamic content that needs to be available at low-latency in a few regions |


## AWS Global Accelerator

AWS Global Accelerator is a service that improves the availability and performance of your applications with local or global users. It provides static IP addresses that act as a fixed entry point to your application endpoints in a single or multiple AWS Regions, and uses the AWS global network to optimize the path from your users to your applications.
