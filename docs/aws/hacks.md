---
title: AWS Hacks
date: 2023-03-22
---
# AWS Hacks & Tips

## Subscribe to feature updates via SNS

Subscribe via AWS CLI:

```bash
aws sns subscribe --topic-arn arn:aws:sns:us-east-1:692768080016:aws-new-feature-updates --protocol email --notification-endpoint mail@example.com --region us-east-1
```
