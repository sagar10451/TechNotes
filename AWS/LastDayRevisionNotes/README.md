# AWS Quick Reference Guide

## 4. IAM Security Tools

- **AWS Credentials Report:** Account level, status of various credentials, password age, MFA enabled
- **IAM Access Advisor:** Revoke permissions which are not being used

## 5. Common Ports

- Port 3389 = RDP
- Port 3306 = Database
- Port 22 = SSH

## 6. EC2 Features

### Placement Groups
- **Cluster:** Extreme low latency and high network throughput, if AZ goes down => all instances fail at same time
- **Spread:** Different hardware rack, reduces risk of simultaneous failure, limited to 7 instances per AZ
- **Partition:** Instances spread across partitions in multiple AZs, 7 partitions in one AZ, 1 partition = 100s of instances, safe from rack failure

### ENI (Elastic Network Interface)
Network card that provides network connection to EC2 instance

### Hibernate Instance
- Instance RAM state is stored in EBS volume
- Must be EBS volume only and must be encrypted
- Must have enough space to contain the RAM
- 60 days maximum

## 7. EBS and EFS

### EBS Volume Types
- **gp3:** 16,000 IOPS, size of volume and IOPS are NOT linked
- **gp2:** 16,000 IOPS, size of volume and IOPS are linked
- **io1:** 64,000 IOPS
- **io2:** 256,000 IOPS, called block express
- **sc1 (HDD):** Cheapest, archive data, infrequent data

### Multi-Attach Feature
io1 and io2 only

### Boot EBS Volume
gp2, gp3, io1, io2

### EFS Modes
**1. Performance Mode:** General purpose, Max I/O
**2. Throughput Mode:** Bursting, Provisioned, Elastic

### EFS Storage Class
Use storage tiers to save cost

**Note:** EBS backup uses I/O, should not run them while application is handling lots of traffic as it may impact performance

## 8. Load Balancers

### NLB (Network Load Balancer)
- Extreme performance, low latency
- TCP, UDP
- Static IPs (1, 2, or 3 static IPs)
- Layer 4 = Transport layer = TCP/UDP
- Works without cookies for sticky session, relies on client's IP address instead
- Operates at Layer 4 (TCP/UDP) - doesn't see HTTP headers/cookies

### GLB (Gateway Load Balancer)
- Layer 3 = Network layer = route packets, packet inspection
- Uses GENEVE protocol on port 6081

### SNI (Server Name Indication)
- Multiple certs on one LB for multiple domains
- Reads hostname to pick correct SSL cert

### Listener
Checks protocol/port (HTTP:80, HTTPS:443)

### Connection Draining
When instance is being removed or marked unhealthy, it is given time to finish any active requests (default 300 seconds)

### Auto Scaling Cooldown
After scaling activity, cooldown of 300 seconds (5 mins). During cooldown period, ASG will not launch or terminate additional instances (to allow metrics to stabilize)

## 9. RDS and Aurora

### RDS Read Replica
- ASYNC replication
- Replicas can be promoted to own DB

### RDS Multi-AZ Setup
- SYNC replication
- No one can read from standby

### RDS Custom
Oracle and Microsoft SQL Server

### Aurora Endpoints
- Write endpoint
- Reader endpoint
- Custom endpoint (no concept of reader endpoint here)

### Babelfish
Lets SQL Server run directly on Aurora PostgreSQL without code changes

### Aurora Backup
Automated backup cannot be disabled

### Aurora Database Cloning
Faster than snapshot and restore because:
- Clone database shares original database volume
- Only new updated data is written to new volume
- Uses copy-on-write protocol

**Comparison:**
- Snapshot/Restore = full data copy from S3 (slow, expensive)
- Clone = shared volume + copy-on-write (fast, cheap)

### RDS Proxy
Allows app to pool and share DB connections

### ElastiCache
- **Redis:** Supports sets and sorted sets (unique elements + ranking)
- **Memcached:** Supports SASL-based authentication

## 10. Route 53

### CNAME
- Maps a hostname to another hostname
- Can't create CNAME for root domain

### TTL
Mandatory for every DNS record, except Alias record

### Alias Record
Always of type A or AAAA

### Route 53 Routing Policies
- **Failover:** Associate primary with health check (mandatory)
- **Geoproximity:** Shift traffic from one region to another using bias value

### Health Check for Private Resources
CloudWatch metric and CloudWatch alarm

### Resolver Endpoint
Entry/exit point for DNS queries between AWS VPC and external network

## 11. Deployment Services

### Golden AMI
Pre-configured AMI with app/dependencies installed - faster EC2 launch

### Elastic Beanstalk
PaaS (Platform as a Service) to deploy apps (just upload code) - handles infrastructure, scaling, monitoring automatically

**Beanstalk Tiers:**
- Web Server (handles HTTP)
- Worker (processes background tasks from SQS)

**Beanstalk Deployment Modes:**
1. Single instance (1 EC2 instance with Elastic IP)
2. High availability with Load Balancer

**Key Difference:**
- Beanstalk = "I want to deploy my app, don't care about infrastructure"
- CloudFormation = "I want to define exact infrastructure setup"

## 12. S3 Features

### S3 Replication
- Async
- Enable versioning to perform replication

### S3 Batch Replication
Old objects don't replicate by enabling replication. To replicate old objects, use S3 Batch Replication

### Glacier Retrieval Times
**Glacier Flexible Retrieval:**
- Expedited = 1-5 mins
- Standard = 3-5 hours
- Bulk = 5-12 hours

**Glacier Deep Archive:**
- Standard = 12 hours
- Bulk = 48 hours

## 13. S3 Advanced Features

### Lifecycle Rule
Apply to whole bucket or prefix

### S3 Requester Pays
Requester must be AWS user

### Request Per Prefix
- 3,500 writes per second
- 5,500 reads per second

### Multi-Part Upload
- Parallel upload
- Must use for files > 5 GB
- Recommended for > 100 MB

### S3 Transfer Acceleration
Speed up upload/download via CloudFront, uses edge locations

### S3 Batch Operations
Perform large-scale actions on many objects

### S3 Storage Lens
Helps with optimization and cost management in S3

### S3 Analytics
Analyzes storage access patterns to recommend when to transition objects from Standard to Standard-IA

## 14. S3 Security

### SSE-KMS
Uses bucket key to reduce API calls, reduces cost and improves performance

### Encryption
- Changing encryption of object creates new version
- Bucket policy is evaluated before encryption setting

### S3 MFA Delete
Versioning must be enabled

### S3 Access Logs
Record all logs made to bucket, store logs in different bucket in same region

### S3 Pre-Signed URL
Secure upload/download without making bucket public

### Glacier Vault Lock
Glacier + object cannot be deleted by admin also

### S3 Object Lock
- Requires versioning
- **Retention:**
  1. Compliance: Not by admin also
  2. Governance: Admin can delete
- **Legal Hold:** Lock until manually removed

### S3 Access Point
Instead of having large complex bucket policy, create access points. Each access point has its own DNS

### S3 Object Lambda
Modify S3 objects on the fly without making copy. Needs S3 Access Point to work

## 15. CloudFront and Global Accelerator

### CloudFront
- CDN
- **Origin:** S3 (OAC), VPC (ALB/NLB/EC2), HTTP endpoints
- VPC origin = hide backend resources

### Global Accelerator
- 2 static Anycast IPs
- No caching
- TCP/UDP

### API Acceleration
Speed up API calls using CloudFront + Lambda@Edge

### Dynamic Site Delivery
Accelerate dynamic web pages using CloudFront

## 16. Storage Services

### AWS Storage Gateway
Connect on-prem environment to AWS storage

**Types:**
1. S3 File Gateway (NFS/SMB)
2. Volume Gateway (iSCSI, snapshot, 2 types: cached (S3 primary), stored)
3. Tape Gateway (iSCSI VTL)

### AWS Transfer Family
Clients can upload/download data from S3/EFS using FTP

### AWS DataSync
Data transfer and sync between on-prem and AWS (AWS to AWS). Scheduled, not continuous

## 18. Container Services

### ECS Cluster Capacity Provider
Instead of using EC2 ASG in ECS, use this

### CSI Driver
Add/delete/mount storage to pods

### AWS App Runner
Rapid production deployment using container images

### AWS App2Container
- Existing app in on-prem -> container conversion
- Supports only Java, .NET
- Generates CloudFormation templates automatically

## 19. Lambda and Serverless

### Lambda Features
- Reserved concurrency
- Cold start
- Provisioned concurrency (solution for cold start)

### Lambda SnapStart
- Initialize function, takes snapshot
- 10x faster than cold start
- Only JAVA

### Lambda Invocation
Triggered by data events (RDS table data changes)

### RDS Event Notification
Triggered by instance-level events

### API Gateway
Managed service that creates, publishes, and manages RESTful/WebSocket APIs as a front door to backend services (Lambda, EC2, etc.)

### AppSync
Build API using GraphQL, different from API Gateway

### DynamoDB Row-Level Security
"Users should only access THEIR OWN data in DynamoDB" → Cognito Identity + IAM LeadingKeys condition

## 21. Database Services

### Amazon Neptune
Graph database with Neptune Streams

## 22. Analytics Services

### Athena Federated Query
Query outside S3 via Lambda connectors

### Redshift
- Analytics
- Data stored as columnar storage instead of rows
- Snapshot is incremental, only changes are saved

### Redshift Spectrum
Query S3 without loading data to Redshift cluster, NOT serverless

### Amazon EMR (Elastic MapReduce)
- Managed Hadoop/Spark clusters for big data processing
- **Nodes:** Master node, Core node, Task node (not long running)

### Amazon QuickSight
- AWS, external, files
- **SPICE Engine:** In-memory engine for files
- **Enterprise Edition:** Groups + column-level security

### AWS Glue
- **Data Catalog:** Runs Glue Data Crawlers
- **Glue Studio:** GUI for ETL jobs
- **Job Bookmarks:** Prevent reprocessing of data
- **DataBrew:** Limited ETL in UI without coding

### AWS Lake Formation
- **Blueprints:** Helps ingest data from multiple sources to data lake

### Amazon MSK
- Kafka
- Retention = 7 days default
- Can be stored to S3 for unlimited days

## 23. AI/ML Services

### Amazon Kendra
Document search engine, ask questions and get answers from text

### Amazon Comprehend
Find insights and relationships in text, sentiment analysis

## 24. Monitoring and Compliance

### CloudWatch Features
- **Log Insights:** Query logs
- **CloudWatch Log Live Tail:** Real-time log streaming in console
- **Unified Agent:** Logs + metrics
- **Contributor Insights:** Find top contributors

### AWS CloudTrail
Who made what changes

**CloudWatch Event Types:**
- Management events (for resources)
- Data events (for invoke, read)
- Insights events (anomaly detection)

### AWS Config
Audit + compliance checking + auto-remediation (via SSM Automation) - detects non-compliant resources and fixes them automatically

## 25. Account Management

### SCP (Service Control Policies)
IAM policy attached to OUs or accounts to restrict users and roles

### IAM Permission Boundaries
Max permissions a user/role can get

### IAM Identity Centre
Single sign-on service for multiple AWS accounts and outside

### Permission Sets
IAM policies in IAM Identity Center

### AD Connector
Just forwards auth requests to on-prem AD

### Simple AD
- Doesn't support MFA
- Cannot integrate with on-prem AD

### Integrating IAM Identity Centre with Active Directory
AWS Managed Microsoft AD or SAML

### AWS Control Tower
Automated setup for multi-account AWS environment with best practices

**Guardrails:**
- Preventive (SCPs)
- Detective (Config)

### AWS Config
Compliance detection

## 26. Security Services

### KMS Key
Per region

### Multi-Region Key
Used in global services

### SSM Parameter Store
- Secure string must be used for passwords/sensitive data
- No rotation supported
- **Advanced:** Supports TTL, incurs charges

### S3 Replication with SSE-KMS
Requires KMS key in both regions

### AWS Secrets Manager
- Supports rotation (can be disabled)
- **Multi-Region Secret:** Used by database replication in multiple regions

### AWS Firewall Manager
Centrally manage firewall rules (WAF, Shield Advanced, Security Groups, Network Firewall) across ALL accounts in AWS Organizations

### Amazon Inspector
Vulnerability scanning (software, network)

### Amazon Macie
Identify PII in S3 buckets

### AWS WAF
- SQL injection, XSS (cross-site scripting)
- Rate limiting, geo restriction
- To get fixed IP => use NLB or Global Accelerator
- Must be placed in front of ALB or CloudFront
- AppSync (for GraphQL) also integrates with WAF

### AWS Shield
- DDoS protection at Layer 3/4
- **Shield Advanced:** Advanced DDoS protection, 24/7 support, $3,000 per month, Layer 3/4/7

**Note:** All AWS edge services (Route 53, CloudFront, Global Accelerator) have Shield Standard protection automatically - no setup needed!

### Amazon GuardDuty
Account-wide threat detection by scanning logs

## 27. VPC and Networking

### Custom VPC
CIDR Block Size Limits: Minimum /28 (16 IP addresses), Maximum /16 (65,536 IP addresses)

### NAT Gateway
Present in public subnet

### Bastion Host
Your laptop → Bastion Host (in public subnet) → Private server (in private subnet)

### NAT Gateway
Provides outbound internet connectivity for private subnet (private subnet → NAT Gateway → IG)

### On-Prem to AWS Connectivity
- Site-to-Site VPN (slow and encrypted)
- AWS Direct Connect (not public network, not encrypted, very fast, but months to setup)
- AWS Client VPN (allows users to access both AWS and on-prem resources)

### VPC Endpoint
Allows private resources in VPC to access other AWS resources like S3

**Types:**
1. Gateway Endpoint (free, only S3 and DynamoDB)
2. Interface Endpoint

### Site-to-Site VPN Components
- Virtual Private Gateway (VGW)
- Customer Gateway (CGW)
- VPN Connection

### Direct Connect Gateway
Enables DX to communicate with multiple VPCs across different AWS regions

### AWS Transit Gateway
Central hub that connects multiple VPCs, Site-to-Site VPN, Direct Connect through one gateway

### Egress-Only Internet Gateway for IPv6
Keeps instances "private" while allowing outbound internet connectivity

### AWS Network Firewall
Protects entire VPC, uses GLB

## 28. Disaster Recovery and Migration

### RPO/RTO
Lower RPO/RTO = More expensive but less data loss/downtime

### 4 DR Methods
1. **Backup and Restore:** Only data stored in S3/Glacier, infrastructure built from scratch, cheapest, slowest
2. **Pilot Light:** Critical services like database are always running
3. **Warm Standby:** Scaled-down infrastructure is always running
4. **Multi-Site (Hot Site):** Full-scale infrastructure running in Active-Active in second region, lowest RPO/RTO, most expensive

### AWS Data Migration Service (DMS)
- Migrate database from on-prem to AWS
- All databases except DynamoDB from on-prem
- Supports continuous replication using CDC (Change Data Capture)
- If heterogeneous, SCT is used (Schema Conversion Tool)

### Infrastructure Migration
- Plan migration using Discovery Service (agentless or run agent)
- **AWS Migration Hub:** Map out what you want to migrate
- **MGN (Migration Service):** Lift and shift, involves Replication Agent

### Continuous Replication
Use Direct Connect or integrate with AWS DataSync (for files) or DMS (for databases)

### VMware Cloud on AWS
Lift and shift VMware workloads to AWS

### RDS and Aurora Migration

**Case 1: AWS RDS MySQL to Aurora MySQL**
- Option 1: Take DB snapshot from RDS MySQL and restore snapshot on Aurora MySQL (DOWNTIME)
- Option 2: Create Aurora read replica from RDS MySQL, when replication lag is 0, promote Aurora read replica as its own DB

**Case 2: On-Prem MySQL to Aurora MySQL**
- Option 1: Use Percona backup and store backup to S3, create Aurora MySQL from S3 backup
- Option 2: Create Aurora MySQL DB, use mysqldump utility to migrate into Aurora (slower than S3 method)
- Option 3: Use DMS if both databases are running to do continuous replication

**Case 3: AWS PostgreSQL to Aurora PostgreSQL**
- Option 1: Take DB snapshot from RDS PostgreSQL and restore snapshot on Aurora PostgreSQL (DOWNTIME)
- Option 2: Create Aurora read replica from RDS PostgreSQL, when replication lag is 0, promote Aurora read replica as its own DB

**Case 4: On-Prem PostgreSQL to Aurora PostgreSQL**
- Option 1: Create backup of on-prem and store it to S3, from S3 create Aurora PostgreSQL cluster
- Option 2: Use DMS if both databases are running to do continuous replication

## 29. High Performance Computing (HPC)

### API Gateway
Can also do caching

### EFA (Elastic Fabric Adapter)
Lowest latency

### FSx for Lustre
Millions of IOPS

### io2 Block Express
High IOPS

### AWS Batch
Running multi-node parallel jobs

### AWS Parallel Cluster
Deploy and manage HPC clusters

### Lifecycle Hooks
Perform actions before instance launches/terminates. ASG uses lifecycle hooks to trigger scripts that take snapshots of EBS volumes, then create new volumes and attach them to new instances

## 30. Additional Services

### AWS CloudFormation
- Define AWS resources in templates (JSON/YAML)
- Automate resource creation/updates/deletion

### CloudFormation Service Role
IAM role that CloudFormation assumes to create/update/delete resources on your behalf

### Amazon Pinpoint
Email, SMS, push notification, voice message, in-app message, marketing campaigns

### AWS Cost Anomaly Detection
Uses ML, identifies one-time cost or continuous cost increases

### AWS SSM Run Command
Execute commands or scripts on multiple instances

### AWS SSM Patch Manager
Automate OS patching

**Note:** Requires SSM Agent installed on instances (pre-installed on Amazon Linux, Ubuntu AMIs)

### AWS Outposts
- AWS infrastructure in your on-premises data center
- Customer is responsible for physical security of rack

### Amazon AppFlow
Integrate SaaS apps (Salesforce, SAP, Zendesk, Slack, ServiceNow) with AWS services

### AWS Amplify
Build and deploy full-stack web/mobile apps, serverless

### Instance Scheduler
Used in CloudFormation to automatically start/stop EC2/RDS instances on schedule, save 70% cost
