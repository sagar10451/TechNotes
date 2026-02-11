# AWS CloudFront and Global Accelerator

## CloudFront
- HTTP/HTTPS protocol
- Caching capability
- Dynamic IP

### CloudFront Origins
- **S3:** With OAC (Origin Access Control)
- **VPC:** ALB/NLB/EC2 instance
- **HTTP Endpoints**

### VPC Origin
- Hides backend resources
- Previously ALB/NLB/EC2 had to be public

### Security
- Allow list
- Block list

### Price Classes
1. **All:** All edge locations
2. **200:** All edge locations except expensive ones (like India)
3. **100:** Cheapest - US, Europe, Canada only

## CloudFront Invalidation

### TTL Settings
- **Default TTL:** 24 hours
- **Min TTL:** 0 seconds
- **Max TTL:** 1 year

We can use path and `*` to invalidate certain content

## Global Accelerator

### Features
- Uses Anycast IP
- Improves application availability, performance, latency
- Avoids multiple hops to reach server
- Uses AWS backbone network
- TCP/UDP protocols
- 2 static Anycast IPs
- No caching
- DDoS protection via AWS Shield

## Use Cases

### API Acceleration
- Speeds up API calls
- Uses CloudFront + Lambda@Edge

### Dynamic Site Delivery
- Accelerates dynamic web pages
- Uses CloudFront
