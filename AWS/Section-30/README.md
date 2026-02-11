# AWS Additional Services

## AWS CloudFormation

Infrastructure as Code (IaC)

### What It Does
- Define AWS resources in templates (JSON/YAML)
- Automate resource creation/updates/deletion
- Repeatable deployments

### Key Concepts
- **Change Sets:** Preview changes before applying
- **Drift Detection:** Detect manual changes to resources

### Features
- Rollback on failure
- Cross-region/cross-account deployments
- StackSets (deploy to multiple accounts/regions)
- Free (pay only for resources created)

### CloudFormation Service Role
- IAM role that CloudFormation assumes to create/update/delete resources on your behalf
- User must have `iam:PassRole` to delegate role to CloudFormation

## Amazon SES (Simple Email Service)

- Used to send and receive emails
- Marketing emails, bulk emails

## Amazon Pinpoint

### Features
- Sending messages across multiple channels: email, SMS, push notification, voice message, in-app message
- Supports receiving replies
- Can send personalized messages
- Used for marketing campaigns

## AWS Cost Explorer

- Check cost of each resource, cost by service
- Monthly, yearly, daily views
- Forecast for 12 months also available

## AWS Cost Anomaly Detection

- Uses ML to identify unusual spending
- Identifies one-time cost or continuous cost increase
- Sends notifications via SNS

## AWS SSM (Systems Manager)

Manage and operate AWS resources at scale

### Key Features

**1. Session Manager:**
- SSH into EC2 without opening ports or bastion hosts

**2. Parameter Store:**
- Store secrets, configs, passwords
- No rotation like Secrets Manager

**3. Run Command:**
- Execute commands or scripts on multiple instances remotely
- Patch, configure, automate tasks

**4. Patch Manager:**
- Automate OS patching

**5. Automation:**
- Automate operational tasks (restart, snapshot, remediation)

**6. Inventory:**
- Collect metadata from instances

**Requires:** SSM Agent installed on instances (pre-installed on Amazon Linux, Ubuntu AMIs)

## AWS Outposts

- AWS infrastructure in your on-premises data center
- Run AWS services locally (EC2, EBS, RDS, ECS)
- Hybrid cloud solution
- Low latency, data residency requirements
- Managed by AWS
- Customer is responsible for physical security of rack

## Amazon AppFlow

### Overview
- Integrate SaaS apps (Salesforce, SAP, Zendesk, Slack, ServiceNow) with AWS services
- No code, fully managed
- Bi-directional data flow
- Schedule or event-driven

### Destinations
- **AWS Destination:** S3, Redshift
- **Non-AWS Destination:** Snowflake, Salesforce

## AWS Batch

### Overview
- Run batch computing jobs at any scale
- Automatically provisions EC2/Spot instances
- Manages job queues, scheduling, compute resources
- Docker container-based
- Can run for very long (Lambda is only for 15 mins)
- Managed service (Lambda is serverless)

### Use Cases
Data processing, ML training, rendering

## AWS Amplify

### Overview
- Build and deploy full-stack web/mobile apps
- Frontend hosting (React, Angular, Vue)
- Backend services (auth, API, storage)
- CI/CD built-in
- Serverless

## Instance Scheduler

### Overview
- CloudFormation-based solution
- Automatically start/stop EC2/RDS instances on schedule
- Save costs by 70% (stop dev/test instances nights/weekends)
- Tag-based scheduling
