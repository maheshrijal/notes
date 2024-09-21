---
title: AWS CloudFormation
date: 2023-03-20
---

# AWS CloudFormation

- Resources are defined in a JSON or YAML file called a template that describes the AWS resources you want to create and the dependencies between them.
- Resources are not created manually, which is a huge time saver & excellent for control

## CloudFormation Cost

- Each resource within a stack is tagged with a identifier called a stack ID so you can track the cost of each stack
- Cost of resource can be estimated using the CloudFormation template
- **Saving strategy**: In Dev, you can delete the stack at 5PM and recreate it at 9AM the next day

## Productivity

- Ability to destroy and recreate infrastructure on the fly
- Automated diagram generation
- Declearative approach to infrastructure: No need to figure out the order of creation

!!! warning
    `Resources` section in a Cloudformation template is the only mandatory section. However, If both `Description` & `AWSTemplateFormatVersion` are present in a template, the `Description` must be below the `AWSTemplateFormatVersion`