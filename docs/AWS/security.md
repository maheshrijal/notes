---
title: AWS Security & Operations
date: 2024-03-28
---

# AWS Security & Operations

## AWS Secrets Manager

- It shares functionality with Parameter Store
- Designed for secrets (Passwords/API Keys)
- Supports **automatic rotation**. This uses lambda
- Directly integrates with some AWS Products (**RDS**)
- Secrets are encrypted using KMS

## Web Application Firewall

### Web Access Control Lists (WEBACL)

- WEBACL default action (Allow or Block) - Non matching
- Resource Type - CloudFront or Regional service
- Add rule grups or rules, processed in order
- Web ACL Capacity Units (WCU) - Default 1500 - Help with complex rules which need to be computed
- Can be increased with support ticket
- WEBACLs are associated with resource (Eg: Cloudfront distributions)
- Adjusting a WEBACL takes less time than associating one

### Rule groups

- Contain rules
- They don't have default actions. It is defined when groups or rules are added to WEBACLs
- Managed (AWS or Marketplace), Managed bu customer or service owned (Eg: Shield, Firewall Manager)
- Rule groups can be referenced by multiple WEBACL
- Have a WCU capacity (defined upfront, default max 1500*)

### WAF Rules

- Type, Statement, Action
- Type: Regular or Rate-based
- Eg: Allow SSH connection from certain IP address, or if anyone attempted to connect via SSH 5000 times in a minute
- Statement: (What to match) or (Count all) or (What & Count)
- Match against: Origin country, IP, label, header, cookies, query parameter, URI Path, Query string, **body (First 8192 bytes only)**, HTTP method
- Single, AND, OR, NOT can be used
- Action: Regular: Allow*, Block, Count, Captcha
- Action: Rate Based: Block, Count, Captcha (No allow with rate based rules)
- Custom Response (**x-amzn-waf-**), Label
- Labels can be referenced later in the same WEBACL. Use for multi stage flows
- Allow & Block stop prcoessing, Count/Captcha actions can continue

### Pricing

- WEBACL - Monthly price for every WEBACL (Can be reused)
- RULE on WEBACL - Monthly price
- REQUESTS per WEBACL - Charged monthly
- Intelligent Threat Mitigation:

    - Bot control: Charged monthly + request based fee
    - Captcha: priced per 1000 login challenge attempts
    - Fraud Control/Account Takeover: Charged monthly + 1000 login attempts
    - Marketplace rule groups - Extra costs

## AWS Shield

- AWS Shield Standard & Advanced - DDOS Protection
- Shield Standard is free, Shield Advanced has a cost
- Network Volumetric Attacks (L3) - saturate capacity
- Network Protocol Attacks (L4) - TCP SYN flood
- Application Layer (L7 attacks) - Web request floods

### Shield Standard

- Free for AWS customers
- Protection at the perimeterm. Region/VPC or AWS edge
- Common Network (L3) or Transport (L4) layer attacks
- Best protection using R53, Cloudfront, AWS Global Accelerator

### Shield Advanced

- Costs 3000 per month (per organization), 1 year lock-in + data OUT
- Protects CloudFront, R53, Global Accelerator, Anything Associated with EIPs, ALBs, CLBs, NLBs
- **Not automatic** - must be explicitly enabled in shield advanced or AWS firewall manager shield advanced policy
- Cost Protection -  *for unmitigated attacks**
- Proactive Engagement & Shield Response Team (SRT)
- WAF Integration - includes basic AWS WAF fees for web ACLs, rules, and web requests
- Application Layer (L7) DDOS Protection (uses WAF)
- Real time visibility of DDOS events and attacks
- Health-based detection - application specific health checks using Route 53 health checks. It is a requirement for using proactive engagemnet team
- Protection groups (Add resources to a group and protect resources at group level)

## CloudHSM

- KMS is Managed but, **KMS is shared across other AWS accounts** but, it seperated
- HSM is **Single Tenant** Hardware Security Module (HSM)
- AWS provisioned. But, fully customer managed. AWS have no access
- HSM is **FIPS 140-2 Level 3** compliant, while KMS is **FIPS 140-2 Level 2** (Some areas of KMS are L3 compliant)
- CloudHSM has industry standard APIs - **PKCS#11**, Java Cryptography Extensions (**JCE**), CryptoNG (**CNG**) libraries
- KMS can use CloudHSM as a **custom key store**, CloudHSM integration with KMS
- Deployed into AWS managed CloudHSM VPC
- HSM is not HA by default (Runs within 1 AZ by default), for HA, create HSM cluster and have 2 HSMs in the cluster(HSMs replicate within the cluster), in the AZ you use.

**Use Cases & Drawbacks**

- No native integration b/w cloudHSM and AWS Service (Eg: No S3 SSE)
- Offload SSL/TLS processing for Web Servers
- Enable Transparent Data Encryption (TDE) for Oracle Databases
- Protect the Private Keys for an Issuing Certifcate Authority (CA)

## AWS Config

- Record configuration changes over time on resources
- Used for auditing of changes, compliance with standards
- Does not prevent changes happening - no protection
- Can check compliance against defined standards
- Regional service. Supports cross-region and cross account aggregation
- Changes can generate SNS notifications & near realtime events via EventBridge and S3 Lambda
- Stores all configuration data & changes in a S3 bucket
- Resources are evaluated againts Config Rules - either AWS managed or custom (using Lambda)


## Amazon Macie

- Data Security and Data Privacy Service
- Discover, Monitor and Protect Data stored in S3 buckets
- Automated discovery of PII, PHI, Finance
- Managed Data Idenitifiers - Built in - ML patterns
- Custom Data Identifiers - Proprietary - Regex based
- Integrates with Security Hub & 'finding events' to Events Bridge
- Centrally manage either via AWS org or on macie account inviting other accounts
- Policy Findings(Eg: encryption turned of on bucket) or Sensitive data findings (Eg: exposed credentials)

## Amazon Inspector

- Scans EC2 instances, the instance OS & containers for **Vulnerabilities** and **deviations** against best practise
- Lengths: 15 minutres, 1 our, 8/12 hours or 1 day
- Provides a report of findings ordered by priority
- Network Assesment (Agentless)
- Network & Host Assesment (Agent) - OS level vulnerabilities - Adding agent adds more information to the scan
- Rules package determine what is checked
- Network Reachability (Agentless)
- Check reachability end to end. EC2, ALB, DC, ELB, IGW, ACLs, RTs, SGs, Subnets, VPCs, VGWs, VPC perring
- Findings: RecognizedPortWithListener, RecognizedPortNoListerner, RecognizedPortNoAgent
- **UnrecognizedPortWithListener** - offered by the **Network Reachability rules (Agentless)** package

**Agent Required**

- Host Assesments package
- Common Vulnerabilities and exposures (CVE) package
- Centre for Internet Security (CIS) benchmarks
- Security best practices for Amazon Inspector

## Amazon GuardDuty

- Continuous security monitoring service
- Analyses supported data source, AI/ML, threat intelligence feeds
- Identifies unexpected and unauthorized activity
- Notify or event driven protection/remediation
- Support multiple accounts (Master and Member accounts)