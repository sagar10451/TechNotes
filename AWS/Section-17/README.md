# AWS Messaging and Integration Services

## Communication Patterns
1. Sync
2. Async

## AWS Decoupling Services
SQS, SNS, Kinesis

## SQS (Simple Queue Service)

### Overview
- **Producer:** Sends message to queue, there can be multiple producers
- **Queue:** Stores data 1-14 days, default 4 days, unlimited throughput
- **Consumer:** Pulls message from queue, processes it, then deletes it
- There can be multiple consumers, 1 message is sent to one consumer
- Once a consumer picks a message, that message becomes invisible to other consumers
- If consumer is able to process within visibility timeout, then consumer deletes the message from queue
- If not, message is again visible in queue for other consumers to process
- **Message Size:** 256 KB

### Auto Scaling
- Consumer can auto scale using `ApproximateNumberOfMessage` metric
- This is a CloudWatch metric, we can add ASG based on this

### Queue Management
- To delete all messages from queue = purge the queue
- **Default Visibility Timeout:** 30 sec, max 12 hours
- Visibility timeout prevents one message from being processed multiple times
- Low visibility timeout = duplicates
- High visibility timeout = delayed reprocessing if consumer crashes

## SQS Long Polling

### Benefits
- Decreases the number of API calls made to SQS
- Increases efficiency and reduces latency of your application
- Keeps connection open up to 1-20 sec
- Immediate delivery

## Short Polling: When to Use
- When we don't want latest data
- Hit API at fixed interval
- Example: checking for updates

## SQS FIFO

### Features
- Strict order
- Exactly-once processing, duplicates removed via Deduplication ID (5 mins window)
- After 5 mins, same ID is accepted again
- Limited throughput
- **Message Group ID:** Order messages within group
- Name ends with `.fifo`

## SNS (Simple Notification Service)

### Overview
- Notification service
- Broadcast message to multiple consumers
- Push model
- 1 message = many subscribers
- Publisher sends message to topic
- One topic can have many subscribers

### Subscribers
- SQS
- Lambda
- Kinesis Data Firehose
- Email
- SMS
- HTTP/S
- Mobile push notification
- EventBridge

### Integration
- Auto integrates with CloudWatch, ASG, S3, RDS, DMS

### SNS Topic Types
1. Topic publish
2. Direct publish for mobile SDK

### Important Notes
- SNS can't save data directly to S3
- No message storage in SNS, immediately delivered

## SNS + SQS Fanout Pattern

### Overview
- All subscribers are SQS (can be others also)
- FIFO support: SNS FIFO + SQS FIFO
- Message filtering in SNS so that we can send what data a subscriber wants

## Kinesis Data Streams (KDS)

### Overview
- Collect and store real-time or streaming data
- **Data:** Max 1 MB per record
- **Retention:** 1 year, auto expire, no manual delete, ability to replay

### Capacity Modes
1. **Provisioned:** Manual shard scaling
2. **On-Demand:** Auto scaling based on past 30 days throughput

### Producers for KDS
- Custom app
- EC2
- Lambda
- Kinesis Agent
- Kinesis Producer Library (KPL) = high throughput, batching, retries
- AWS SDK
- IoT devices

### Consumers for KDS
- Lambda
- KDF (Kinesis Data Firehose)
- EC2
- Custom app
- Kinesis Client Library (KCL)
- Kinesis Data Analytics
- AWS Managed Flink

### Consumer Types
1. **Pull Data:** 2 MB/shard
2. **Enhanced Fanout:** Push data: 2 MB/consumer

## Amazon Kinesis Data Firehose (KDF)

### Overview
- Used to load data
- Consumes data from Kinesis Data Streams
- Near real-time as it uses buffer

### Sources for KDF
- Apps
- Kinesis Agent
- KDS
- CloudWatch
- IoT

### Destinations for KDF
- S3
- Redshift
- OpenSearch
- Custom HTTP endpoint
- Datadog
- Splunk
- New Relic

### Features
- Can do transformation (CSV/JSON -> Parquet/ORC)
- Compression (gzip/snappy)
- Data is not stored in KDF
- KDF is serverless
- Firehose has no replay capacity
- **Default Buffer:** 5 MB, buffer interval 5 mins

## Amazon MQ

### Overview
- Managed service for RabbitMQ and ActiveMQ
- Protocols: MQTT, AMQP, STOMP, OpenWire, WSS
- Does not scale as much as SQS/SNS
- Supports both queue (like SQS) and topics (like SNS)
- Scaling limited by broker/server resource
- Multi-AZ: active + standby
- Uses EFS for shared storage
