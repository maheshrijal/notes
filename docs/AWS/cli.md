---
title: AWS CLI Commands
---

# AWS CLI Commands

## EC2

### List

```
aws ec2 describe-instances
```

### Metadata

```
curl -s http://169.254.169.254/latest/meta-data/
```

## S3

### List

**List bucket contents**
```
aws s3 ls
```

**List recursively**

```
aws s3 ls s3://YOUR_BUCKET --recursive --human-readable --summarize
```

### Copy

**Copy file to S3**

```
aws s3 cp MyFolder s3://bucket-name — recursive [–region us-west-2]

```

**Copy file from S3**

```
aws s3 cp s3://my-bucket/ <local directory path> --recursive --exclude "*" --include "<prefix>*"
```

**Copy Bucket to Another Bucket**

```
aws s3 sync s3://source-bucket/ s3://destination-bucket/
```


### Remove

**Delete bucket with all of it's contents**

```
aws s3 rb s3://bucket-name –force
```

**Selectively delete bucket contents**

```
aws s3 rm s3://bucket-name --recursive --exclude "*" --include "*.tmp"
```

**Empty the bucket**

```
aws s3 rm s3://bucketname --recursive
```

### Share

**Pre sign a S3 object URL**

```
aws s3 presign s3://DOC-EXAMPLE-BUCKET1/mydoc.txt --expires-in 604800
```

or

```
aws s3 presign s3://DOC-EXAMPLE-BUCKET1/mydoc.txt --expires-in 604800 --region ap-south-1 --endpoint-url https://s3.af-south-1.amazonaws.com
```

!!! note "S3 Endpoints"
    S3 endpoint URLs are in the format: `http://bucket-name.s3-website.Region.amazonaws.com`

### Download

Downlad a S3 file with Pre-Signed URL

```
wget -O filename ""
```

## IAM

### List

**List all IAM Users**
```
aws iam list-users
```
