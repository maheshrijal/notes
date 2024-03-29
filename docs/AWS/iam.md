---
title: AWS IAM
date: 2023-01-24
---
# AWS IAM (Identity & Access Management)

## IAM Users & Groups

Groups let us specify permission for multiple users. Identity based policies can be attached to a group

- Users are people in AWS & can be grouped.
- User don't have to belong to a group, & they can also belong to multiple groups
- IAM user group is a collection of IAM users.
- User **groups cannot be nested** & logged into.
- Users or Groups can be assigned JSON documents called policies. These documents define permissions.
- IAM Groups have no user limits. All 5000 users can be part of the IAM group. Also, there is no built in all user group in IAM.

!!! warning

    Groups are not a true identity. Groups cannot be referenced as a principal in a policy. A resource policy cannot grant access to a IAM group.

## IAM Roles

Used when AWS services need to perform action on users behalf. Permissions to AWS Services are assigned with IAM Roles. Policies are attached to one principal, however, Roles can be asssumed by anyone.

Roles can be used by:

- IAM user in the same AWS account as the role
- IAM user in a different AWS account
- A web service offered by AWS like EC2
- An external user authenticated by an external identity provider

Example Roles:

- EC2 instance roles
- Lambda function roles
- Cloudformation roles

## IAM Policies

Policies are used to manage access in AWS. Policies can be attached to IAM Identities (User, Group or roles). Policies are evaluated when an IAM Principle (user / role) makes a request.

### Policy Types

#### Identity Based

##### Inline Policy

Policies that are added to single user, group or role. Maintain a strict one-to-one relationship between a policy & entity. They are deleted when you delete the idenitity.

- Only attached to a user

##### Managed Policy

- AWS Managed Policies: Created & managed by AWS

- Customer managed policies: Created and managed by AWS account


#### Resource Based

- Attached to resources

Eg IAM Policy

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ListObjectsInBucket",
            "Effect": "Allow",
            "Action": ["s3:ListBucket"],
            "Resource": ["arn:aws:s3:::bucket-name"]
        },
        {
            "Sid": "AllObjectActions",
            "Effect": "Allow",
            "Action": "s3:*Object",
            "Resource": ["arn:aws:s3:::bucket-name/*"]
        }
    ]
}
```

### IAM Policy Priorites

If policy json contains same permissions for the resources, this comes into effect. Lower the number higher the priority.

1. Explicit Deny
2. Explicit Allow
3. Default DENY (Implicit)

## IAM Security Tools

### Credential Report: Account Level
- A report that lists all account users & status of their credentials


### Access Advisor: User-level
- Shows service permissions granted to a user and when those services were last accessed

## Service Control Policies

- SCPs can be applied to the organization, to OU's or to individual accounts. 
- Policies inherit down a tree (Eg: Root > OU > AWS Accounts)
- These policies limit what the account (including root user) can do. The root user cannot be restricted. But, by restricting an account, the root user is also restricted.
- SCPs DON'T GIVE permission - they just control what an account CAN and CANNOT grant via identity policies.

!!! warning 

    Management account is never impacted by Service Control Policies.