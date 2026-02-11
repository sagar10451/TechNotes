# AWS EC2 (Elastic Compute Cloud)

## EC2 Instance Metadata Service
**169.254.169.254:**
- EC2 Instance Metadata Service endpoint
- Only accessible from within EC2 instance
- Returns instance info (ID, IAM role, IP, etc.)
- Standard across cloud providers

## EC2 Components
- EC2 instance
- EBS volume
- ELB (Elastic Load Balancer)
- ASG (Auto Scaling Group)

## Creating EC2 Instance

### Configuration Steps
1. **Choose AMI** = Select OS (example: Amazon Linux is Ubuntu)
2. **Instance Type** = CPU, RAM, choose architecture (x86 or ARM)
3. **Instance Details** = Number of instances, network settings (VPC and subnet), IAM roles
4. **Storage** = EBS, EFS, Instance store
5. **Tags** = Metadata labels
6. **Security Group** = Firewall rules
7. **Key Pair** = For SSH login

### Architecture Types
- **x86** = Intel or AMD
- **ARM** = Graviton

### User Data
Script that runs with sudo command at startup to download updates, install updates, etc.

## EC2 Instance Types

1. **General Purpose** = Balanced compute, memory, and networking
2. **Compute Optimised** = Newman instance, c7i.2xLarge, High CPU to memory ratio
3. **Memory Optimised** = High RAM, database, in-memory caching, name starts with R
4. **Accelerated Computing** = GPU, ML, video rendering
5. **Storage Optimised** = High I/O performance, big data, data warehousing
6. **HPC Optimised** = For HPC (High Performance Computing)

## Security Groups

### Overview
- Firewall for EC2, outside EC2
- One security group can be attached to multiple instances
- One EC2 can also have multiple security groups attached
- Recommended to create one security group just for SSH

### Rules
- **Inbound Rules** = Traffic which goes inside EC2 (by default all inbound traffic is blocked)
- **Outbound Rules** = Traffic which goes out of EC2 (by default all outbound traffic is authorized)

## IP and CIDR

### IP Address
Example: 203.3.112.5

### CIDR = Range of IPs
- **/8** = Very big network
- **/16** = Big network
- **/24** = Small network
- **/28** = Very small network
- **/30** = Tiny network
- **/32** = Single IP

### Source and Destination for Security Group
- IP
- CIDR
- Anywhere
- Another Security Group

### Another Security Group
Allows traffic from instances that belong to different security group within same VPC.

**Example:** Web server needs to talk to database server. Set inbound rule on database server SG which will allow traffic from web server.

## Famous Ports
- **Port 22** = SSH
- **Port 22** = SFTP
- **Port 80** = HTTP
- **Port 443** = HTTPS
- **Port 3306** = Database
- **Port 21** = FTP
- **Port 3389** = RDP

## EC2 Instance Connect vs Session Manager

### Comparison
- Both allow to use EC2 terminal
- **EC2 Instance Connect** = You need to open port 22 in Security Group
- **Session Manager** = You can connect without opening port 22, i.e., connect without SSH

## EC2 Purchasing Options

### 1. On Demand
- Unpredictable workload

### 2. EC2 Reserved Instances
- Commit to an instance
- Less flexible
- Fixed: instance type, region, tenancy, OS
- 1-3 year commitment
- 72% discount
- Reserved in region or AZ

### 3. Convertible Reserved Instance
- Same as above, but more flexible
- Can change: instance type, instance family, OS, scope, tenancy
- 66% discount

### 4. EC2 Savings Plans
- Commit to amount you can spend per hour
- More flexible
- 72% discount
- Locked to instance family and AWS region

### 5. EC2 Spot Instance
- 90% discount
- Instance can be taken anytime

### 6. EC2 Dedicated Host
- Entire physical server reserved for you
- BYOL (Bring Your Own License)
- Control placement, cores, sockets
- Most expensive

### 7. EC2 Dedicated Instance
- Runs on hardware dedicated to you
- No control over instance placement

### 8. EC2 Capacity Reservation
- Reserve on-demand capacity in specific AZ for some time
- In this time, even if you don't run instance, you will be charged

## How Spot Instance Works
1. You define max price
2. If your max price is greater than current market price, you get spot instance
3. Anytime market price goes above your max price, you get 2 mins to decide if you want to stop instance or terminate instance

### Spot Block
- Reserve spot instance for 1-6 hours without being interrupted

### Types of Spot Fleet Request

**1. One-Time Request**
- Request is met, then instance is terminated and request is closed

**2. Persistent Request**
- Every time condition meets, your instance will start
- You have to cancel spot request then terminate instance to stop being charged

## Spot Fleet
- Collection of spot instances + optionally on-demand instances
- Submit spot fleet request
- Giving multiple instance types, sizes, and prices
- Spot fleet automatically chooses the best mix of instances to reach target capacity
- Helps in better reliability and cost optimization
