# Cloudfront & Global Accelerator

## Cloudfront

Cloudfront is  a **CDN**, it improves application performance by caching content in edge locations. It consists of.

- Cloudfront integrates with AWS Certificate Manager(ACM) to use SSL certificates
- Cloudfront is for downloads only. It does not perform write caching.  Any uploads go directly to the origin for processing

!!! info "DDOS Protection"
    Cloudfront offers DDOS protection as it is worldwide service & is integrated with Shield, AWS WAF.

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

### Behavior

- The distribution contains the configuation deployed to the edge locations
- A distribution can have many behaviours which are configured with a path pattern. If requests match that pattern, that behaviour is used otherwise default is used.
- Origins, Origin groups, TTL, Protocol Policies, restricted access are cofnigured via Behaviours


### TTL & Cache Invalidation

- More frequest cache HITS = lower origin load
- Default TTL (Configured in Behaviour) = 24 hours (Validate period)
- You can set Minimum TTL & maximum TTL values
- Origin Header: **Cache-Control max-age** (seconds)
- Origin Header: **Cache-Control s-maxage** (seconds)
- Origin Header: **Expires** (Date & Time)
- Headers can be set using Custom origin or S3 (via object metadata)

If the backend origin is updated, Cloudfront doesn't know about it until the TTL has expired. We can force an entire or partial cache refresh bypassing the TTL by performing a Cloudfront Invalidation.

- Cache invalidation is performed on a distribution
- Applies to all edge locations but, takes time
- Invalidate all files: `*`
- Invalidate special path: `/images/*`

- Cache Invalidations cost the same irrespective of the number of objects in a bucket
- Versioned file names can be used instead of Cache Invalidation Eg: cat_v1.jpg, cat_v2.jpg


<!-- ## AWS Certficate Manager - ACM -->

<!--
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

1. **Price Class All**: All regions - best performance
2. **Price Class 200**: Most regions, but excludes the most expensive regions
3. **Price Class 100**: Only the least expensive regions


## AWS Global Accelerator

AWS Global Accelerator is a service that improves the availability and performance of your applications with local or global users. It provides static IP addresses that act as a fixed entry point to your application endpoints in a single or multiple AWS Regions, and uses the AWS global network to optimize the path from your users to your applications.

<!-- - Leverage the AWS internal network to route to your application
- 2 Anycast IP are created for your application
- The Anycast IP send traffic directly to edge locations
- Edge locations send traffic to your application through private network -->

<!-- **Unicast IP** : One server holds one IP address

**Anycast IP** : All servers hold the same IP address & the client is routed to the nearest server. --> -->

<!-- - Works with Elastic IP, EC2 Instances, ALB, NLB, public or private
- Consistent performance
- Health Checks
  - Performs application health checks
  - Helps make your application global
  - Great for disaster recovery

- Security
  - Only 2 external IP need to be whitelisted
  - DDoS protection through AWS Shield -->
