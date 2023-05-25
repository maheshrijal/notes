---
title: AWS CLI EC2 Commands
date: 2023-01-21
---

# AWS CLI: EC2

List all ec2 instances
```
aws ec2 describe-instances
```

EC2 instance metadata

```
curl -s http://169.254.169.254/latest/meta-data/
```