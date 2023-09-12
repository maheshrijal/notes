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


### Cloudfront Geo-Restriction

Restrict access to cloudfront based on IP location. The country is determined by using a third party Geo-IP database.

**Allowlist**: Allow users to access content if they're in one of the countries on a list of approved countries.

**Blocklist**: Prevent users from accessing your content if they're in on of the coutnries on a list of banned countries.

### Pricing

Cloudfront edge locations are all around the world & cost of data out per edge location is different.

**Price Classes**

Reduce the number of edge locations for cost reduction.

1. Price Class All: All regions - best performance
2. Price Class 200: Most regions, but excludes the most expensive regions
3. Price Class 100: Only the least expensive regions

### Cache Invalidation

If the backend origin is updated, Cloudfront doesn't know about it until the TTL has expired. We can force an entire or partial cache refresh bypassing the TTL by performing a Cloudfront Invalidation.

- Invalidate all files: `*`
- Invalidate special path: `/images/*`

## AWS Global Accelerator

AWS Global Accelerator is a service that improves the availability and performance of your applications with local or global users. It provides static IP addresses that act as a fixed entry point to your application endpoints in a single or multiple AWS Regions, and uses the AWS global network to optimize the path from your users to your applications.
