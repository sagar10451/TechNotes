# AWS Route 53 and DNS

## What is DNS?
DNS translates domain names to IP addresses
- Example: www.google.com => 172.17.18.36

## Domain Registrar
Examples: Amazon Route 53, GoDaddy
- This is where we register domains

## Amazon Route 53

### Overview
- Highly available, fully managed, authoritative DNS
- Authoritative means customer can update DNS records
- It's a domain registrar
- Has ability to check health of your resources
- Only AWS service which provides 100% availability SLA

## Route 53 Records

### Creating Records
- We create records in hosted zone
- Each record contains:
  1. **Domain/Subdomain Name** = example.com
  2. **Record Type** = A or AAAA
  3. **Value** = 12.34.56.78
  4. **Routing Policy** = How Route 53 responds to queries
  5. **TTL** = Record caching time (default 5 mins, 300 seconds)

### Record Types
A, AAAA, CNAME, NS

**A:**
- Maps a hostname to IPv4 address
- Example: example.com => 1.2.3.4

**AAAA:**
- Maps a hostname to IPv6 address

**CNAME:**
- Maps a hostname to another hostname
- You don't point directly to IP like A or AAAA, but you point to another domain
- This another domain should be A or AAAA because at the end it should point to IP
- Example: CNAME value could be ALB DNS name, ALB will have value of type A or AAAA
- Can't create CNAME for root domain, as it is A or AAAA and it points to IP
  - Root domain = example.com
  - Subdomain = www.example.com or blog.example.com

**NS Record (Name Server):**
- NS don't map to IP address directly
- They specify which servers are authoritative for that domain and can answer queries about it

## Route 53 - Hosted Zone

### Overview
- Contains records that define how traffic is going to be routed for domain and its subdomains

**Public Hosted Zone:** Allows anyone from internet to query your records

**Private Hosted Zone:** Only queries within VPC

## Route 53: Registering a Domain

### Steps
- Check if domain you want to register is available
- If available, you can buy this for $12-13 per year
- Duration = 1 to 10 years
- You can auto-renew or not auto-renew
- Once you get domain name, you can see domain name inside hosted zone
- Initially you see NS records and SOA record
- NS record: It says you should use AWS DNS to answer DNS queries

## Route 53 - Creating Your First Record

### Steps
1. Hosted zone -> select domain which you bought (sagar.com) -> click on create record
2. Give record name (test.sagar.com)
3. Record type A
4. Value = 11.22.33.44
5. TTL = 300 seconds
6. Routing policy = simple routing
7. Create the record

### How It Works
When we type: test.sagar.com => it asks hosted zone what is the value of this URL => hosted zone will return 11.22.33.44

## Route 53: EC2 Setup

### Configuration
- 3 EC2 in 3 different regions and one ALB
- EC2 in Frankfurt, N. Virginia, Singapore
- Launch ALB in Frankfurt
- For subnet for ALB choose: 3 AZs in Frankfurt
- Listener = protocol = HTTP = port 80 = forward to target group

### Target Group
- For now just add Frankfurt EC2 in it

### Testing
1. First check individual EC2 instance is working or not using their public IPv4 address
2. Then take ALB DNS name and paste in browser, it should give Frankfurt EC2

## Route 53 TTL

### Overview
- Idea is we don't want to query DNS too often
- **High TTL (24 hours)** = Less traffic on Route 53, but outdated record
- **Low TTL (60 sec)** = More traffic on Route 53, more cost
- TTL is mandatory for every DNS record, except: Alias record

### Test How TTL Works
- In target group add all 3 EC2 instances
- Then hit ALB DNS name in browser
- Let's say TTL is 60 sec
- Now if ALB responds Frankfurt EC2, then for 1 min every time you hit ALB DNS it will give you Frankfurt EC2 only. Only after 60 sec, it might go to other EC2

## CNAME vs Alias

### Overview
- When you have AWS resource like load balancer or CloudFront, it exposes hostname
- You want to map this hostname to domain name
- Example: DNS name of Load Balancer = internal-k8s-nssnp-omeninte-ec84ddc80e-729931459.us-west-2.elb.amazonaws.com, which is mapped to: online.streaming-signals-np.nielsen.com

### You Can Do Above Thing in 2 Ways:

**1. CNAME:** Hostname to another hostname
- Example: k8s-monitori-promethe-4e757d6a26-343795632.us-west-2.elb.amazonaws.com => prometheus.streaming-signals-np.nielsen.com

**2. Alias:** Specific to Route 53
- Example: k8s-nssnp-omenexte-e077fa3fcf-1162732465.us-east-1.elb.amazonaws.com. => online.streaming-signals-np.nielsen.com

## Alias

### Features
- Alias works for root domain also
- Maps hostname to AWS resource (ELB, CloudFront, S3 bucket, etc.)
- Alias record is always of type A or AAAA
- TTL is set automatically by Route 53
- It evaluates target health also

## Route 53 Routing Policies

**Note:** Route 53 does not route traffic, it just responds to DNS queries

### Types

**1. Simple:**
- Single resource
- Can specify multiple values in same record
- In case of multiple records, random record is chosen by client
- With alias, only one AWS resource supported
- No health check feature

**2. Weighted:**
- To make it work, DNS name must have same name and type
- Health check supported
- Testing new app version by sending small traffic to it
- Record having higher weight gets more traffic

**3. Failover:**
- Active-passive: one primary instance and one secondary instance
- Associate primary with health check (mandatory)
- If primary instance is going to be unhealthy, failover will happen to secondary instance

**4. Latency Based:**
- Redirect traffic to EC2 which has least latency (user and AWS region)
- Health check supported

**5. Geolocation:**
- Routing is based on where user is actually located
- Can specify location by continent, country, state
- Use case: website localization, restrict content

**6. Multi-Value:**
- Receive up to 8 records back
- Health check supported

**7. Geoproximity:**
- Shift traffic from one region to another using bias value
- To expand (1-99), to shrink (-1 to -99)

**8. IP-Based Routing:**
- In Route 53, define two locations with two different CIDR blocks
- When user comes, direct to one of the locations

## Health Check

### Overview
1. **Global Health Checkers** = Total 15, if > 18% health checkers report it healthy, then Route 53 considers it healthy
2. **Calculated Health Check:** Multiple health checks, use AND, OR, NOT to combine

### How to Monitor Health of Private Resource?
1. Route 53 health checkers are outside VPC
2. So we create CloudWatch metric and CloudWatch alarm to check health status, check alarm itself

## Difference Between Domain Service and Domain Registrar

**Domain Registrar:** Buy or register domain here
- Examples: GoDaddy, Route 53

Domain registrar usually provides domain service to manage DNS records, but we can use another DNS service also.

**Example:** Buy domain in GoDaddy, use Route 53 to manage records

For this, update the name server records on GoDaddy to use Route 53 name servers

## Resolver Endpoint

### Overview
Entry/exit point for DNS queries between AWS VPC and external network

**Inbound Endpoint:**
- Lets on-prem network resolve AWS private DNS names
- If on-prem needs to access AWS resources, inbound endpoint makes it possible

**Outbound Endpoint:**
- Lets AWS VPC send DNS queries to on-prem DNS server
- Useful if AWS resources need to resolve names that exist on your on-prem network
