# EC2 Networking

## Public IP vs Private IP

### Public IP
- Unique across whole web
- No two machines on public internet can have same public IP
- Can be geo-located

### Private IP
- Not accessible through internet
- Only identified within private network
- IP must be unique across private network
- Two private networks can have same private IP
- Machine connects to internet through NAT + Internet Gateway
- Only specific range of IPs can be used as private IP

### Note on SSH to EC2
When you do SSH to EC2 machine, you can't use private IP because you are not in same network unless you use VPN.

When you set up VPN, you configure same IP range (CIDR block) as your VPC. This way you can connect to VPC network from your other device as well when connected on VPN.

## Elastic IP
- If you need fixed public IP, then you need to have Elastic IP
- You can attach to one instance at a time
- Max 5 Elastic IPs per account (can increase by raising request)
- Can be used for failover

## Placement Groups

Placement of instances with respect to one another on hardware in same AZ.

### 1. Cluster
- Same AZ, same hardware rack
- 10 Gbps bandwidth
- Extreme low latency and high network throughput
- Great for computational jobs
- If AZ goes down => all instances fail at same time

### 2. Spread
- Same AZ, different hardware rack
- Reduces risk of simultaneous failure
- Limited to 7 instances per AZ per placement group
- High availability
- Can span across AZ but need multiple placement groups

### 3. Partition
- Instances spread across partitions in multiple AZs
- Partition 1 and Partition 2 in one AZ, and Partition 3 in different AZ
- Up to 7 partitions per AZ
- One partition can have 100s of EC2 instances
- Safe from rack failure
- Good performance

## ENI (Elastic Network Interface)

### Overview
- Virtual network card
- When you create EC2 instance, ENI is created and attached by default
- You can also attach existing ENIs
- They provide network connection to EC2 instance
- You can create independent ENIs and attach or move them to other EC2 instances on the fly for failover
- ENIs are bound to AZ
- You can attach multiple ENIs to one instance

### Important Notes
- SG is attached to ENIs and not EC2 instance as a whole
- Elastic IP also resides inside ENIs only

## EC2 Hibernate

### Instance States

**Stop Instance**
- Data on EBS is kept intact

**Terminate Instance**
- By default, data on root EBS volume gets deleted
- Delete on termination attribute is enabled
- Can be disabled from console if needed

**Hibernate Instance**
- Instance RAM state is stored in EBS volume
- Instance shuts down
- When you start instance again, it reloads saved RAM state

### Requirements
- Must be EBS volume only
- Must be encrypted
- Must have enough space to contain the RAM

### Use Cases
- Long-running processes

### Limitations
- Not supported for bare metal instances
- Instance cannot be hibernated more than 60 days
