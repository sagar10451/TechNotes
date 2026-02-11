# AWS VPC and Networking

## Default VPC

- Automatically created for new accounts
- Uses /16 CIDR block
- NACL allows all inbound and outbound by default
- Best practice: create custom VPC for production

## Custom VPC

- You get 1 VPC per region initially
- Can create 5 VPCs per region
- Make sure VPC CIDR does not overlap
- **CIDR Block Size Limits:**
  - Minimum: /28 (16 IP addresses)
  - Maximum: /16 (65,536 IP addresses)
- Can have 5 CIDR blocks inside VPC

## Subnets in VPC

- Subnet is smaller IP range used to place AWS resources in single AZ
- Subnet is tied to single AZ

### 2 Types of Subnets

**1. Public Subnet:**
- Resources which can be accessed by internet
- Route table must contain route to Internet Gateway

**2. Private Subnet:**
- Resources which can be accessed only within VPC
- Route table has no route to Internet Gateway

### Private Subnet Internet Access Flow
Step 1: Private subnet → NAT Gateway (in public subnet)
Step 2: NAT Gateway → Internet Gateway → Internet

### Important Notes
- AWS reserves 5 IP addresses in every subnet that cannot be assigned to instances
- Best practice: use small CIDR block for public subnet and larger CIDR block for private network

## Internet Access in Subnets

- One Internet Gateway can be attached to only one VPC
- Attaching IG does not grant internet access to public subnet
- Must update route table also
- If above conditions are met, then subnet is public

### Verifying Internet Access for EC2
- Subnet setting must have auto-assign public IPv4 enabled
- Attach IG to VPC
- Update route table

## Bastion Host

- Dedicated EC2 instance placed in public subnet that is used to access private subnet servers
- Connect to Bastion Host first (from internet)
- Then connect from Bastion Host to private servers

### Flow
Step 1: Your laptop → Bastion Host (in public subnet)
Step 2: Bastion Host → Private server (in private subnet)

## NAT Instance

- EC2 instance in public subnet that allows EC2 instances in private subnet to access internet
- Can use as bastion host (dual purpose)
- Not highly available by default (single point of failure)
- Uses security groups

## NAT Gateway

- AWS-managed NAT service (fully managed by AWS)
- Provides outbound internet connectivity for private subnet (private subnet → NAT Gateway → IG)
- Don't need to configure security group

## NACL and Security Groups

Two firewalls in VPC:

**Security Groups:**
- Stateful: if inbound traffic is allowed, then outbound is automatically allowed
- Only support ALLOW rules
- Default SG blocks inbound traffic

**NACL:**
- Stateless: inbound and outbound rules are independent
- Support both ALLOW and DENY rules
- Default NACL allows all traffic

## On-Prem to AWS Connectivity

Connect on-prem servers to AWS:

**1. Site-to-Site VPN:**
- Provides encrypted tunnels
- Highly available
- Lower bandwidth (1.25 Gbps)

**2. AWS Direct Connect:**
- Bypass public internet
- Fast (100 Gbps)
- Requires months to deploy
- Traffic not encrypted by default

**3. AWS Client VPN:**
- Allows individual remote users to connect to VPC
- Supports AD and SAML integration
- Allows users to access both AWS and on-prem resources

## VPC Peering

- Connect two VPCs
- VPC CIDR must not overlap
- Not transitive
- Manually update route table in both VPCs to route traffic to each other
- Can connect any two VPCs in other regions, other accounts, etc.

## VPC Endpoint

Allow private resources in VPC to access other AWS resources like S3

### 2 Types

**1. Gateway Endpoint:**
- Only S3 and DynamoDB
- Works by adding route in subnet routing table that targets gateway endpoint ID
- FREE

**2. Interface Endpoint:**
- S3, DynamoDB, and others
- Provisions ENI with private IP
- Requires Security Group to control access to ENI
- INCURS CHARGES

**Note:** NAT Gateway route is removed from private subnet route table

## VPC Flow Logs

- Logs sent to S3 for long-term storage
- Logs sent to CloudWatch for streaming, where logs can be monitored and alerted

## Site-to-Site VPN

### 4 Main Components
1. **Virtual Private Gateway (VGW):** AWS side attached to VPC
2. **Customer Gateway (CGW):** Your on-premises router
3. **VPN Connection:** The actual encrypted tunnel between VGW and CGW
4. **Customer Gateway Device:** Physical device in your data center with public IP

## Direct Connect and DX Gateway

**Direct Connect Gateway:** Enables DX to communicate with multiple VPCs across different AWS regions

You can use DX for main network and as a fallback have Site-to-Site VPN

## AWS Transit Gateway

- Central hub that connects multiple VPCs, Site-to-Site VPN, Direct Connect through one gateway
- Only AWS service that supports IP multicast

## VPC Traffic Mirroring

- Copy network traffic from ENIs for inspection
- Traffic is sent to another ENI, NLB, GLB
- Source and target must be either in same VPC or different VPCs connected through VPC peering

## Egress-Only Internet Gateway for IPv6

- NAT Gateway equivalent for IPv6 - outbound only
- IPv6 instances can initiate connections to internet
- IPv6 only (IPv4 uses NAT Gateway)

### Why Needed?
- All IPv6 addresses are public (no private IPv6)
- Keep instances "private" while allowing outbound

## AWS Network Firewall

- Protects entire VPC
- Layer 3-7 protection
- Inspects traffic in all directions
- Uses Gateway Load Balancer internally
- Rules can be added to allow, block, or alert
