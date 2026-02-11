# AWS RDS, Aurora, and ElastiCache

## RDS (Relational Database Service)

### Database Engines Supported by AWS RDS
1. PostgreSQL, MySQL, MariaDB, Oracle, Microsoft SQL Server, IBM DB2
2. Aurora

### Advantages of Using RDS vs Deploying Own DB on EC2
There are many advantages

**Note:** You can't do SSH to RDS because it's a managed service

## RDS Auto Scaling

### Overview
- When RDS detects you are running out of storage, it scales automatically
- You can set maximum storage threshold (max limit for DB storage)

### Automatically Modify Storage If:
1. Free storage is less than 10%
2. Low storage lasts for 5 mins
3. 6 hours have passed since last modification

Useful for workloads with unpredictable workloads

## Difference Between Read Replica and Multi-AZ Setup

### 1. RDS Read Replica

**Features:**
- Helps you scale reads
- Can have up to 15 read replicas within AZ, cross-AZ, cross-region
- Replication is ASYNC
- Replicas can be promoted to own DB
- Application must update connection string to use read replicas

**Use of Read Replica:**
- Doing analytics without affecting production load
- Only SELECT (read) happens from read replicas

**Network Cost in Read Replicas:**
- No cost in RDS read replica within same region
- But if you are going cross-region, you have to pay

### 2. RDS Multi-AZ Setup

**Features:**
- Multi-AZ is mainly for disaster recovery
- SYNC replication to standby replication
- One DNS name, in case there is problem with master, auto failover happens to standby database
- Standby database is only for standby, no one can read from it

**Note:** Read replica can be set up as multi-AZ for disaster recovery

## How to Make RDS Database Go from Single AZ to Multi-AZ?

- It should be zero downtime, no need to stop DB
- Just click on modify the database and enable multi-AZ

### Following Things Happen Internally:
1. Snapshot is taken by RDS automatically
2. A new DB is restored from the snapshot in new AZ
3. Synchronization is established between two databases

## RDS Custom

### Overview
- Get access to underlying system
- It is of 2 types: Oracle and Microsoft SQL Server
- With RDS Custom, we can configure settings, install patches, enable native features, access underlying EC2 instance using SSH
- To perform any customization, it is recommended to deactivate automation mode
- Better to take DB snapshot for recovery

## Amazon Aurora

### Overview
- Aurora is of AWS, not open source
- Aurora is compatible with PostgreSQL and MySQL
- Storage grows in increments of 10 GB up to 128 TB
- It can have up to 15 read replicas
- Failover is instant

### Aurora High Availability and Read Replica
- It stores 6 copies of data across 3 AZs
- Master + up to 15 read replicas = per region, but master is present only in one AZ

### How Aurora Works?
- Only master writes to storage
- Because master can fail, Aurora provides you writer endpoints (DNS name which always points to master)
- You also have reader endpoint = helps with load balancing and connects automatically to read replicas
- Automatic patching with 0 downtime
- Restore data to any point in time using backups
- Auto backup and recovery
- Auto failover

### Aurora Custom Endpoint
- 2 different types of read replicas: one is less powerful machine and one is more powerful machine
- On the more powerful machine, we can run analytics
- When using custom endpoint, reader endpoint is not used

### Aurora Serverless
- No capacity planning needed
- Good for unpredictable workloads
- Client is going to talk to proxy fleet managed by Aurora
- In the backend, many Aurora instances will be created based on workloads

### Global Aurora
- 1 primary region (read/write)
- Can set up up to 10 secondary regions (read only)
- In each region, can have up to 16 read replicas
- Typically cross-region replication takes less than 1 second

### Aurora Machine Learning
- Supported: SageMaker, Comprehend

### Babelfish
Feature that lets SQL Server run directly on Aurora PostgreSQL without code changes

## RDS Backup

### 1. Automated Backup (Expires)
- Daily backup
- Transaction logs are backed up every 5 mins
- Ability to restore to any point in time
- 1 to 35 days of retention, set it to 0 to disable automated backup

### 2. Manual DB Snapshot
- Manually done
- Retention of backup as long as you want

## Aurora Backup (Different from RDS Backup)

### 1. Automated Backup
- 1 to 35 days, cannot be disabled

### 2. Manual DB Snapshot
- Manual process
- Retention of backup as long as you want

## RDS and Aurora Restore Options (From On-Prem to AWS RDS)

Restoring a RDS/Aurora backup or snapshot creates new database

### 1. Restoring MySQL RDS Database from S3
1. Create a backup of your on-prem
2. Store it on S3
3. Restore the backup onto new RDS instance running MySQL

### 2. Restore MySQL Aurora from S3
1. Create a backup of on-prem data using Percona XtraBackup
2. Store the backup file in S3
3. Restore the backup onto new Aurora cluster running MySQL

## Aurora Database Cloning

### Overview
- Allows you to create new Aurora DB cluster from existing one

### Cloning is Faster Than Snapshot and Restore. Why?
- Because it uses copy-on-write protocol
- Clone database shares original database volume
- Only new updated data is written to new volume

## RDS and Aurora Security

- Master not encrypted, read replicas cannot be encrypted
- To encrypt an unencrypted database, go through DB snapshot and restore as encrypted
- IAM auth: use IAM roles to connect to your database
- Audit logs can be enabled, if you want it for longer, send it to CloudWatch

## RDS Proxy

### Features
- Allows app to pool and share DB connections
- It collects queries then hits main RDS instance at once
- Reducing stress on database
- Serverless, autoscaling, highly available (multi-AZ)
- Reduces Aurora failover by 66%
- No code change required
- RDS is only accessible within VPC, not public
- Use AWS Secrets Manager for storing credentials and all

## ElastiCache

### Overview
- Managed Redis or Memcached
- In-memory database
- Makes your app stateless by storing user data
- You have to make application code changes
- Cache must have an invalidation strategy

## Redis vs Memcached

### Redis
- Multi-AZ
- Read replicas
- Backup and restore feature
- Supports sets (unique items) and sorted sets (unique items + automatic sorting by score)

### Memcached
- Multi-node for partitioning of data (sharding) = separate portion of data based on row range
- Not highly available
- Non-persistent
- Backup and restore only for serverless version
- Multithreading architecture

## ElastiCache Authentication

- ElastiCache supports IAM auth only for Redis
- For others, it uses username, password
- You have security within Redis also called Redis Auth

### Memcached
- Supports SASL-based authentication

## 3 Kinds of Patterns for Loading Data into ElastiCache

1. **Lazy Loading:** When cache miss, read happens from DB, it is also written to cache
2. **Write Through:** Write to cache then immediately to DB
3. **Session Data:** Store session data using TTL
