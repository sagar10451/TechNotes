# AWS Storage and Data Transfer Services

## Snow Family

### Device Types
1. **Snowcone:** Small, includes DataSync agent
2. **Snowball:** Medium
3. **Snowmobile:** Huge

## AWS Snowball

### Overview
- Data transfer + edge compute
- Move petabytes of data
- On-prem to AWS and AWS to on-prem

### Device Types
1. **Storage Optimised:** 210 TB
2. **Compute Optimised:** 28 TB, truck on road, ship

### How It Works
1. Request Snowball device via console
2. Load data
3. Ship back to AWS
4. AWS loads data to S3

### Security and Access
- **Service Role:** IAM role that AWS service assumes to perform action on your behalf
- Snowball uses service role to access S3 bucket during import/export
- Data is encrypted + tamper resistant

**Note:** Snowball import to Glacier directly not supported, apply S3 lifecycle policy

## Amazon FSx

Managed file system on AWS

### 1. Windows File Server
- SMB/NTFS protocol
- AD integration
- Multi-AZ
- SSD/HDD

### 2. Lustre
- HPC/ML workloads
- S3 integration
- SSD/HDD
- High IOPS and throughput
- Lustre is parallel distributed file system

### 3. NetApp ONTAP
- NFS/SMB/iSCSI
- Auto scaling
- Snapshot, compression, deduplication, cloning
- For NAS migration

### 4. OpenZFS
- NFS only
- High IOPS
- Snapshot, compression
- No deduplication
- Cloning supported

## FSx Storage Types

### 1. Scratch File System
- Temporary storage
- Data is not replicated
- High burst
- High-speed processing

### 2. Persistent File System
- Long-term storage
- Data replicated in same AZ
- Lower latency

**Note:**
- Lustre supports both scratch file system and persistent file system
- Other 3 only have persistent file system

## AWS Storage Gateway

Connect on-prem environment to AWS storage

### Use Case
- Use when on-prem needs AWS storage
- Translates NFS/SMB/iSCSI to HTTPS for cloud

### Storage Gateway Types

**1. S3 File Gateway**
- NFS/SMB is converted to HTTP
- Stored in S3
- Local cache
- Lifecycle to Glacier for archive

**2. Volume Gateway**
- iSCSI -> stored as EBS snapshots in S3
- 2 types:
  1. **Cached:** S3 primary, local cache
  2. **Stored:** On-prem primary, async backup to S3

**3. Tape Gateway**
- iSCSI VTL => store to S3/Glacier
- Replace physical tapes for backup/archive

### Important Notes
1. To access bucket, create IAM role for each gateway type
2. Gateway is installed on on-prem data centre

## AWS Transfer Family

### Overview
- With this, clients can upload/download data from S3/EFS using FTP/FTPS/SFTP protocol
- User access directly via endpoint

## AWS DataSync

### Overview
- On-prem to AWS
- AWS to AWS
- Data transfer and sync between on-prem and AWS
- Uses on-prem agent (NFS, SMB, HDFS)
- Destination: S3, EFS, FSx
- It is scheduled, not continuous
- AWS to AWS transfer without agents
