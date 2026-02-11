# AWS Disaster Recovery and Migration

## Disaster Recovery

### Key Metrics

**RPO (Recovery Point Objective):**
- How much data loss is acceptable
- Example: RPO = 1 hour → lose max 1 hour of data

**RTO (Recovery Time Objective):**
- How long to restore service after disaster
- Example: RTO = 4 hours → system back in 4 hours

**Note:** Lower RPO/RTO = More expensive but less data loss/downtime

### 4 Disaster Recovery Methods

**1. Backup and Restore:**
- Only data is stored in S3/Glacier
- Infrastructure is built from scratch
- Cheapest, slowest

**2. Pilot Light:**
- Critical services like database are always running

**3. Warm Standby:**
- Scaled-down infrastructure is always running

**4. Multi-Site (Hot Site):**
- Full-scale infrastructure is running in Active-Active in second region
- Lowest RPO/RTO
- Most expensive

### Key Points
1. For backup: use CRR (Cross-Region Replication)
2. For DNS failover: use Route 53 to switch to secondary
3. Auto recovery using CloudFront/Lambda

## AWS Data Migration Service (DMS)

### Overview
- Migrate database from on-prem to AWS
- Supports homogeneous migration and heterogeneous migration
- Supports continuous data replication
- Database remains available throughout the process

### Source and Target
- **Source:** All databases except DynamoDB
- **Target:** Everything

### Process
- Requires DMS replication instance (EC2) which performs replication task
- Supports continuous replication using CDC (Change Data Capture)
- If heterogeneous, SCT is used (Schema Conversion Tool)
- DMS is multi-AZ
- AWS SCT is installed on on-prem database

## RDS and Aurora Migration

### Case 1: AWS RDS MySQL to Aurora MySQL

**Option 1:**
- Take DB snapshot from RDS MySQL and restore snapshot on Aurora MySQL
- DOWNTIME

**Option 2:**
- Create Aurora read replica from RDS MySQL
- When replication lag is 0, promote Aurora read replica as its own DB

### Case 2: On-Prem MySQL to Aurora MySQL

**Option 1:**
- Use Percona backup and store backup to S3
- Create Aurora MySQL from S3 backup

**Option 2:**
- Create an Aurora MySQL DB
- Use mysqldump utility to migrate into Aurora (slower than S3 method)

**Option 3:**
- Use DMS if both databases are running to do continuous replication

### Case 3: AWS PostgreSQL to Aurora PostgreSQL

**Option 1:**
- Take DB snapshot from RDS PostgreSQL and restore snapshot on Aurora PostgreSQL
- DOWNTIME

**Option 2:**
- Create Aurora read replica from RDS PostgreSQL
- When replication lag is 0, promote Aurora read replica as its own DB

### Case 4: On-Prem PostgreSQL to Aurora PostgreSQL

**Option 1:**
- Create backup of on-prem and store it to S3
- From S3, create Aurora PostgreSQL cluster

**Option 2:**
- Use DMS if both databases are running to do continuous replication

## On-Prem Strategy with AWS

What services AWS offers for on-prem migration:

1. **VM Import/Export:** Ability to download AMI as a VM
2. **AWS Application Discovery Service:** Find out what needs to be migrated
3. **DMS:** Migrate on-prem database to AWS
4. **AWS Replication Migration Service:** Incremental replication of on-prem to AWS

## AWS Backup

### Overview
- Centrally manage and automate backups
- No need to create custom scripts or manual processes
- Supports on-demand and scheduled backups
- Supports disaster recovery features like cross-region and cross-account backups
- Backups can be stored in Vault Lock

### AWS Backup Plan

**Creating Backup Plan:**
- Contains backup rules
- Frequency (daily, monthly)
- Retention period
- Backup vault
- Lifecycle rules to move to cold storage

**Assign Resources to Backup Plan:**
- Best way is to filter which resources are going to be backed up by tags

## Cloud Migration Use Case and Services

You have on-prem and want to migrate to cloud

### 1. Plan Your Migration Using AWS Discovery Service

**2 Types of Discovery Service:**

**1. Agentless Discovery Using Connector:**
- Info around VM, CPU, memory, etc.

**2. Run Agent (AWS Application Discovery Agent):**
- More information like networks

Resulting data can be viewed in AWS Migration Hub. Above services help to map out what you want to migrate.

### 2. Migrate Using MGN (Migration Service)

- MGN = Lift and shift
- Involves Replication Agent which performs continuous replication to low-cost staging area in AWS
- During final cutover, production-ready EC2 instance is launched

## Data Transfer Methods into AWS

Transfer large amounts of data into AWS:

**1. Public Internet/Site-to-Site VPN:**
- Slowest option
- Internet speed is very slow
- Immediate to setup

**2. AWS Direct Connect (DX):**
- Dedicated high-speed connection
- 1 Gbps
- Best choice for ongoing, high-volume, secure transfers
- Takes 1 month to setup

**3. AWS Snowball:**
- FASTEST
- Takes one week in total end-to-end

**4. For Continuous Replication:**
- Use Direct Connect
- Integrate with AWS DataSync (for files) or DMS (for databases)

## VMware Cloud on AWS

### Overview
Lift and shift VMware workloads to AWS

- When you have on-prem data center managed by VMware and want to extend to AWS
- Management is still done by VMware
- Leverage computing power and storage from data center to cloud
- Migrate VMware-based workloads to AWS
