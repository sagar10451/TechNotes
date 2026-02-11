# AWS Storage: EBS, AMI, and EFS

## EBS Volume (Elastic Block Store)

### Overview
- Persists data even after instance termination
- Can be mounted to one instance at a time
- Bound to specific AZ
- To get back data: create instance with same EBS volume
- Since it is network-driven, there is latency
- EBS volume can be detached from one instance and attached to other instance very quickly
- To use EBS volume in other AZ: take snapshot -> copy snapshot to target -> create volume from snapshot
- One EBS volume cannot be attached to 2 instances at a time
- 2 EBS volumes can be attached to one instance

### Delete on Termination Attribute

1. **Root EBS Volume** = By default this is enabled, so once instance gets terminated, data is gone. You can disable it from UI.
2. **Non-root EBS Volume** = By default this attribute is disabled.
3. You cannot delete root volume
4. Actual data is stored in non-root volume

## EBS Snapshots

### Features
- Creates backup at any point in time
- Not necessary to detach EBS volume from instance to take snapshot, but recommended
- Can copy snapshots across AZ or region
- Can move snapshot to Archive tier to save cost
- Takes 24 to 72 hours to restore snapshot from archive
- Can setup recycle bin and its retention period if you accidentally delete EBS snapshot

### Fast Snapshot Restore
- Forces full initialization of snapshot to have no latency on first use
- This feature costs a lot of money
- Use this only if you want to create instance very quickly

## AMIs (Amazon Machine Images)

### Overview
- AMI provides faster boot because all your software is pre-installed
- AMIs are built for specific region but can be copied across other regions also
- You can launch instance using: public AMI, your own AMI, AMI present in AWS marketplace

### Make Your Own AMI
1. Start EC2
2. Customize it (install what all you need)
3. Stop instance
4. Build AMI (this will create snapshot)
5. Launch instance from created AMI

## EC2 Instance Store

### Characteristics
- High performance as it is physical drive, not network drive
- Better I/O than network drive (better than io1 and io2 also)
- Data lost on stop, terminate, or hardware failure
- IOPS in millions

## EBS Volume Types

6 types: gp2, gp3, io1, io2, st1, sc1

**Note:** Only gp2, gp3, io1, io2 can be used as boot volume

**IOPS** = Input/Output Operations Per Second

### General Purpose: gp2/gp3

**gp3 (SSD):**
- 3,000 IOPS baseline (up to 16,000)
- 125 MB/s throughput (up to 1,000 MB/s)
- 1 GB - 16 TB
- Size of volume and IOPS are NOT linked

**gp2 (SSD):**
- 3 IOPS per GB (min 100, max 16,000)
- 250 MB/s throughput
- 1 GB - 16 TB
- Size of volume and IOPS are linked

### Provisioned IOPS SSD: io1 and io2

You manually provision/specify exact IOPS needed. Guaranteed consistent performance for databases and critical apps.

**io1 (SSD):**
- 50 IOPS per GB (max 64,000)
- 1,000 MB/s throughput
- 4 GB - 16 TB
- 99.9% durability

**io2 (SSD):**
- 500 IOPS per GB (max 256,000), also called block express
- 4,000 MB/s throughput
- 4 GB - 64 TB
- 99.999% durability

### HDD Volumes

**st1 (HDD):**
- Max 500 IOPS
- 500 MB/s throughput
- 125 GB - 16 TB

**sc1 (HDD):**
- Max 250 IOPS
- 250 MB/s throughput
- 125 GB - 16 TB
- Cheapest, archived data, infrequent data

## Multi-Attach Feature
- Multi-attach feature is only for io1 and io2 EBS volume types, that too in single AZ
- Total IOPS capacity is distributed across EC2 instances
- Only up to 16 EC2 instances at a time

## EBS Encryption

### Overview
- Enable encryption if you want EBS volume to be encrypted (by default it is disabled)
- Snapshots from encrypted volumes are automatically encrypted
- If you take snapshot of unencrypted EBS volume, then snapshot will also be unencrypted
- But we can later copy the unencrypted snapshot and enable encryption during the copy operation

### Encrypt Unencrypted EBS Volume
1. Create snapshot of unencrypted volume
2. Copy snapshot with encryption enabled (new encrypted snapshot gets created)
3. Create new encrypted volume from encrypted snapshot
4. Detach old volume, attach new encrypted volume
5. Delete old volume and original snapshot

## EFS (Elastic File System)

### Overview
- Can be mounted to multiple EC2s, EC2s can be in different AZs also (remember io1 and io2 also support multi-attach feature but only in same AZ, also only 16 EC2 instances max)
- Expensive
- No need to provision capacity in advance
- Control to EFS can be done by Security Group
- EFS is compatible only with Linux (POSIX) based AMIs

### EFS Modes

**1. Performance Mode**
- **General Purpose:** Very low latency
- **Max I/O:** High number of concurrent connections, more latency

**2. Throughput Mode** (amount of data transferred per sec)
- **Bursting:** Default mode, throughput scales with the amount of data stored
- **Provisioned:** You can set throughput regardless of storage size
- **Elastic:** Automatically scales throughput up and down based on workload. Used for unpredictable workload.

### EFS Storage Class

Use storage tiers to save cost. This is a lifecycle management feature just like S3 lifecycle. Allows you to move files to different tiers after N number of days.

**Tiers:**
1. **Standard Tier:** For frequently accessed data
2. **Infrequent Access (EFS-IA):** Cost to retrieve but lower cost to store
3. **Archive Tier:** Rarely accessed data

### Availability and Durability

EFS offers 2 main storage classes:
1. **Standard:** Data is multi-AZ
2. **One Zone:** Data only in one AZ

## Important Note

EBS backup uses I/O. You should not run them while your application is handling a lot of traffic as it may impact performance.
