# AWS Monitoring and Compliance Services

## CloudWatch Metrics

### Overview
- CloudWatch provides metrics for every service in AWS
- Near real-time streaming metric: KDF -> S3
- Retention: 15 months
- Default interval: 5 mins, can enable 1 min interval
- Stream all metrics or subset by namespace filtering

## CloudWatch Logs

### Overview
- First define log group, within log group you have multiple log streams
- Retention: 1 to 10 years
- To query logs, use CloudWatch Log Insights
- Aggregate logs from multiple accounts
- Export to S3

### CloudWatch Log Live Tail
- Real-time log streaming in console
- Filter logs as they arrive
- Debug/troubleshoot live issues
- No need to refresh
- 1 hour free daily

## CloudWatch Agent

### Overview
- By default, no logs from EC2 machine will go to CloudWatch
- You need to run CloudWatch agent to push log files

### Agent Types
1. Logs Agent: Only logs
2. Unified Agent: Logs + metrics

## CloudWatch Alarm
Used to trigger notifications on any metric

## Amazon EventBridge
- Schedule serverless cron jobs in cloud
- React to a service doing something using rules in real-time

## CloudWatch Insights and Operational Visibility

### 1. Container Insights
- Collect, aggregate, and summarize metrics/logs of containers
- For Kubernetes, you have to use agent

### 2. Lambda Insights
- Metrics for Lambda

### 3. Contributor Insights
- Find top contributors

### 4. Application Insights
- Auto-discover app components
- Monitors all components

## AWS CloudTrail

### Overview
- History of who made what change
- Enabled by default (90 days history)
- For long-term retention, send logs to S3 or CloudWatch

### CloudTrail Event Types

**1. Management Events**
- Events that are done on resources
- Create, delete resources

**2. Data Events**
- High volume
- S3 GetObject, Lambda invoke

**3. Insights Events**
- You have to enable and pay for it
- Anomaly detection and sends alerts

### Integration
To intercept any API call, use EventBridge with CloudTrail. When anything changes and that change is present in EventBridge rules, you get notified.

## AWS Config

### Overview
Tracks resource configuration and compliance

### Key Features
- Per-region service (can aggregate multi-region/multi-account)
- Records resource configurations over time
- History stored in S3
- Integrated with CloudTrail (who made changes)
- Evaluates compliance against Config Rules
- Auto-remediation via SSM Automation or Lambda
- Alerts via EventBridge or SNS

### Config Rules
- Define desired configuration state
- AWS Managed Rules: Pre-built (e.g., "s3-bucket-encryption-enabled")
- Custom Rules: Lambda-based
- Evaluate resources: compliant or non-compliant

### Example Flow
Rule: "S3 buckets must have encryption"
- Config checks
- Flags violations
- Triggers SNS alert
- Auto-fix via SSM
