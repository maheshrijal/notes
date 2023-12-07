---
title: Container on AWS
date: 2023-02-10
---

# Containers - ECS, Fargate, ECR & EKS

## ECS - Elastic Container Service

**Container Definition** is a pointer to where the container is stored & the port exposed.

**Task Definition** represents a self-contained application. It can have one or many containers defined. It represents the application as a whole.

Task definition also stores:

- The resources used by the tasks (CPU, Memory)
- Networking mode
- Compatiblity (EC2 mode or Fargate)
- **Task Role (IAM Role that a task can assume)**

**Service Definition** is a service that defines task scaling,  copies to run, capacity & resilience, load balancer etc

### Launch Types / Cluster Mode

**EC2 Mode**

- You must provision & maintain the infrastructure
- Each EC2 instance must run the ECS agent in the ECS cluster
- AWS takes care of starting/stopping containers
- You manage the container host, capacity & availablility
- Can use reserved instnaces or spot instnaces (everything managed by customer)

**Fargate Mode**

- AWS manages the infrastructure (No EC2 instances to manage)
- Serverless
- AWS runs ECS tasks based on the CPU & Memory requirements
- To scale up/down, increase the number of tasks

### IAM Roles for ECS

**EC2 Instance Profile - EC2 launch type**

Used by ECS agent to:

- Make API calls to ECS service
- Send container logs to CloudWatch Logs
- Pull docker image from ECR
- Reference sensitive data in Secrets Manager or SSM Parameter Store

**EC2 tasks Role - EC2 launch type & Fargate launch type**

- Allows each task to have a specific role
- Use different roles for different ECS services
- Task role is defined in task definition

### Load Balancer Integration

**Application Load Balancer** - Supported for most use cases

**Network Load Balancer** - Recommended for high throughput & high performance or to pair with AWS Private Link

### Data Volumes - EFS

- Mount EFS file system to ECS tasks
- Works for both EC2 & Fargate launch types
- EFS is a shared file system, so multiple tasks can access the same file
- Tasks running in any availability zone can access the same file

Use cases: Persistent multi AZ storage for containers

!!! note
    Fargate + EFS = Serverless

!!! warning
    S3 cannot be mounted as a file system to ECS tasks

### ECS Autoscaling

- Automatically increase/decrease the number of tasks based on
    - CPU Utilization
    - Memory utilization
    - ALB Request Count Per Target - Metric coming from the ALB
- ECS Auto Scaling uses AWS Application Auto Scaling

**Target Tracking** - Scale based on a target value for a specific CloudWatch metric

**Step Scaling** - Scale based on a specified CloudWatch Alarm

**Scheduled Scaling** - Scale based on a specified date/time

!!! note
    ECS Service Auto Scaling (task level) != EC2 Auto Scaling (instance level). Fargate Auto Scaling is much easier to setup than EC2 Auto Scaling


## EKS - Elastic Kubernetes Service

- AWS Managed Kubernetes
- Control plane scales and runs on multiple AZs
- Integrates with AWS Services (ECR, ELB, IAM, VPC)
- ETCD distributed across multiple AZs
- Nodes can be **self managed**(self managed ec2), **managed node groups**(EKS handles provisioning & lifecycle management) or **Fargate**(Provision, Configure, Scale automatically)
- Storage Providers include EBS, EFS, FSx