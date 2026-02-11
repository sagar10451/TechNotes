# AWS Container Services

## Docker
Platform to package apps + dependencies into lightweight, portable containers that run consistently anywhere.

## AWS Container Deployment Options
1. ECS
2. EKS
3. App Runner

## Amazon ECS (Elastic Container Service)

### Overview
When you launch container in AWS, you basically launch ECS task on ECS cluster

### Launch Types

**1. EC2:**
- You manage instances
- Each instance must run ECS agent to register instance in ECS cluster

**2. Fargate:**
- Serverless
- Just create task definitions, define CPU/RAM
- AWS takes care of starting and stopping containers

### Data Persistence on ECS
- EFS is used for this
- S3 cannot be mounted as file system in ECS

## IAM Roles Used in ECS

1. **ECS Service Role:** Allows ECS control panel to manage AWS resources like load balancer and auto scaling
2. **Task Execution Role:** Used by ECS to pull image from ECR and send logs to CloudWatch
3. **Task Role:** Used by application to access AWS services
4. **EC2 Instance Profile Role:** Provides permissions to EC2 instance (Fargate does not use this)

## Where ECS Task Runs

1. **Fargate:** Serverless
2. **Fargate Spot:** Cheaper, can be interrupted
3. **Run on EC2:** ASG managed

## ECS Auto Scaling

Scales number of tasks, not instances

### Scaling Policies
1. **Target Tracking:** Maintain 60% CPU
2. **Step Scaling:** Add/remove tasks in steps based on threshold

### ECS Can Scale Based On:
- CPU utilization
- Memory utilization
- ALB request count per target
- Custom CloudWatch metric

**Note:** In case of EC2, instead of using normal ASG for auto scaling, we can use ECS cluster capacity provider.

## Example Flow
S3 -> S3 Event Notification -> EventBridge -> Fargate ECS Task -> Process Object -> DynamoDB

### Additional Notes
- ECS service can also consume SQS messages
- EventBridge can monitor ECS task, then it can trigger SNS notification

## Amazon ECR (Elastic Container Registry)

### Overview
Store and maintain Docker images on AWS

### Types
1. **Public**
2. **Private:** IAM controlled

### Features
- Internally uses S3 to store images
- ECS pulls image from ECR
- Management features: image versioning, vulnerability scanning, lifecycle rules to clean up old images

## Amazon EKS (Elastic Kubernetes Service)

### Overview
- Kubernetes is open source
- Alternative to ECS
- Multi-AZ
- **Worker Nodes:** Runs pods, can be public/private via load balancer
- **Pods:** Smallest deployment unit, similar to ECS task

### Launch Modes

**1. Fargate:**
- Serverless
- AWS managed
- No visible nodes

**2. Managed Node Group:**
- AWS managed EC2 instances in ASG
- Supports on-demand and spot instances

**3. Self-Managed Nodes:**
- User managed EC2 instances
- Full control
- Supports on-demand and spot instances

### When to Use EKS?
When company is already using Kubernetes on-prem and wants to migrate to AWS and wants to keep using Kubernetes

**Note:** Hear "pod" = think of Amazon EKS

## Storage on EKS

Uses CSI driver which automates pod storage lifecycle

**CSI Driver:** Creates/attaches/mounts/deletes storage for pods

### Storage Options
- **EBS:** Block storage, per pod, only EC2, no Fargate
- **EFS:** Shared storage, both EC2 + Fargate
- **FSx (Lustre/Windows/ONTAP):** No OpenZFS file system supported here, high performance, both EC2 and Fargate
- **S3:** Object storage, both EC2 and Fargate
- **Ephemeral:** Temp storage, not persistent, local pod memory

## AWS App Runner

### Features
- Deploy app from source code or container image
- Auto scaling, no infrastructure to manage
- Built-in high availability and load balancing
- Automatic encryption
- VPC for private resources
- **Use Case:** Very quickly deploy web app, do rapid production deployment

## AWS App2Container (A2C)

### Overview
On-prem to AWS

### Features
- CLI tool to containerize existing apps
- Works for Java and .NET web apps into Docker containers
- Discovers which apps need to be migrated
- Creates Docker images
- Stores Docker images in ECR
- Generates CloudFormation templates automatically during containerization for ECS task, EKS pods, network compute
- **Deployment:** Supports ECS, EKS, App Runner
