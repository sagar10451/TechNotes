# AWS Serverless Services

## Serverless Services
Lambda, S3, API Gateway, Cognito, SQS, SNS, Fargate, Aurora Serverless, DynamoDB, KDF, CloudFront

## AWS Lambda

### Limits
- **Max Execution Time:** 15 mins
- **Max Memory:** 10 GB

### Free Tier
- 1 million requests/month
- 400,000 GB-seconds compute time/month

### Features
- Can run cron jobs
- Lambda Execution Role required

### Concurrency
- **Concurrent Execution:** 1000 per region
- **Reserved Concurrency:** Pre-allocated capacity

### Cold Start
- Initial delay when Lambda creates new execution environment
- **Provisioned Concurrency:** Solves cold start problem

### Throttling
- **Sync Invocation:** 429 error, retry
- **Async Invocation:** Retry till 6 hours then DLQ, exponential backoff

### Lambda SnapStart
- Function pre-initialized
- Takes snapshot of memory/disk state
- Restores from snapshot -> directly go to invoke state
- Only for Java
- 10x performance improvement

## Edge Functions

### 1. CloudFront Functions
- JavaScript
- Simple tasks
- Millions RPS
- Native deployment

### 2. Lambda@Edge
- Longer execution
- Node.js, Python
- More powerful
- Thousands RPS
- Bot mitigation, real-time image transformation
- Deployed in us-east-1

## Lambda in VPC

### Why Deploy Lambda in VPC?
High concurrency Lambda + RDS = use RDS Proxy to avoid "too many connections" error

## Lambda Invocation
- Supported by RDS PostgreSQL, Aurora MySQL
- Triggered by data events

## RDS Event Notification
- Triggered by instance-level events
- Delivered via SNS or EventBridge

## DynamoDB

### Performance
- **DynamoDB:** Milliseconds
- **DAX:** Microseconds, caches individual objects
- **ElastiCache:** Stores aggregation results (joins, etc.)

### Streams
- **DynamoDB Streams:** 24 hours data retention
- **Kinesis Data Streams:** 1 year retention

### Global Tables
- Active-active replication
- Uses DynamoDB Streams

### TTL (Time To Live)
- Add a column in DynamoDB to auto-expire data
- Mostly used for user session handling

## API Gateway

### Features
- Expose any AWS service, HTTP, Lambda, WebSockets, etc.
- **Security:**
  - IAM (for AWS users)
  - Cognito (outside users)
  - Lambda Authorizers (custom logic)
- Throttling, create API keys, security

### Deploy API Gateway

**1. Edge:**
- Global deploy via CloudFront
- When using edge optimized, certificate must be in us-east-1

**2. Regional:**
- Local region
- Optional CloudFront
- Certificate must be in API Gateway region

**3. Private:**
- Inside VPC via ENI

## AWS Step Functions

### Features
- Visual workflow diagrams
- Supports almost all AWS services
- Human approval steps supported
- Retry, timeout, parallel execution, error handling

## Amazon Cognito

### 1. Cognito User Pool
- Authentication

### 2. Cognito Identity Pool
- Authorization
- Temporary credentials to access AWS resources

## Row-Level Security in DynamoDB

Using IAM policy with Cognito Identity ID:

**Use Case:** "Users should only access THEIR OWN data in DynamoDB" → Cognito Identity + IAM LeadingKeys condition
