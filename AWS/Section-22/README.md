# AWS Analytics and Big Data Services

## Amazon Athena

### Overview
- Serverless ad-hoc querying tool for any logs originating from AWS services
- Logs: VPC logs, Load Balancer logs, CloudTrail logs stored in S3
- Must setup a result bucket where results will be saved
- Used with QuickSight to create reports/dashboards
- **Pricing:** $5 per TB of data scanned

### Cost Optimization
- Use columnar data for cost saving (less scan)
- Use Glue to convert data to Parquet or ORC from CSV
- Compress data for smaller retrieval
- Partition folders to do less object scanning
- Use larger files >= 128 MB to minimize overhead

### Federated Query
Feature of Athena to query outside S3 via Lambda connectors (AWS or on-prem)
- Uses data source connectors that run on Lambda
- **Sources:** DynamoDB, RDS, Aurora, CloudWatch, on-prem
- Results stored in S3 bucket

## Amazon Redshift

### Overview
- Based on PostgreSQL but not used for OLTP, instead OLAP
- 10x better performance than other data warehouses
- Scales to PB of data
- For analytics and Business Intelligence
- Data stored as columnar storage instead of row
- Has parallel query engine to speed up queries

### Athena vs Redshift
- **Redshift:** Requires provisioning
- **Athena:** Serverless

### Data/Disaster Recovery
- To load data to Redshift: copy data from S3, Firehose, JDBC
- Snapshot is incremental, only changes are saved

### Snapshot Types
**1. Automated:**
- Every 8 hours, every 5 GB
- Set your own retention

**2. Manual:**
- No retention

**Note:** You can set Redshift to copy snapshots automatically to another region

### Redshift Spectrum
Query S3 without loading data to Redshift cluster

## Amazon OpenSearch

### Overview
- Search and analytics engine
- In DynamoDB you can only query by primary key or index, but in OpenSearch you can query any field
- Partial match supported
- **Deployment:** Provisioned and serverless both present
- Does not support SQL by default but can install plugin
- **Visualization:** OpenSearch Dashboard

### Patterns
1. DynamoDB -> DynamoDB Streams -> Lambda -> OpenSearch
2. CloudWatch Logs -> Subscription Filter -> Lambda or Firehose -> OpenSearch
3. KDS -> Firehose or Lambda -> OpenSearch

## Amazon EMR (Elastic MapReduce)

### Overview
- Helps you create Hadoop cluster (Big Data) to analyze and process vast amounts of data
- Runs Spark, HBase, Flink

### Nodes in EMR

**1. Master Node:**
- Manages cluster, manages health of other nodes
- Long running => use Reserved Instance for cost saving

**2. Core Node:**
- Runs tasks and stores data
- Long running => use Reserved Instance for cost saving

**3. Task Node:**
- Runs tasks
- Use Spot Instance for this

## Amazon QuickSight

### Overview
Used to create dashboards

### Data Sources
**AWS:** RDS, Aurora, Redshift, Athena, S3, OpenSearch, Timestream

**External:** SaaS, JDBC, Salesforce, Jira

**Files:** Can import files like CSV, Excel, JSON, TSV

### SPICE Engine
- In-memory engine
- Used only for imported data (files)
- Not used for live DB connections

### Editions
**Free Edition:** Users only

**Enterprise Edition:** Groups + column-level security => prevent some columns from displaying in dashboard

## AWS Glue

### Overview
- Serverless Extract, Transform, and Load (ETL) tool
- Converts Parquet format to columnar data format, CSV to Parquet
- **Triggered by:** S3 events/EventBridge, Lambda

### Data Catalog
Runs Glue Data Crawlers
- Auto-discovers schemas and tables from S3, RDS, DynamoDB, JDBC
- Writes all metadata to Data Catalog
- Used by Glue jobs, Athena, Redshift Spectrum, EMR to perform ETL

### Features
- **Glue Studio:** GUI for ETL jobs
- **Job Bookmarks:** Prevent reprocessing of data
- **Streaming ETL:** Run ETL jobs as streaming jobs instead of batch jobs
- **DataBrew:** Limited ETL in UI without coding

## AWS Lake Formation

### Overview
- Helps you create data lake
- Data lake is built on top of AWS Glue
- **Data Lake:** Contains raw, unstructured data coming from multiple sources
- All data stored in S3 only, later can be analyzed
- Fast deployment (days instead of months)
- Automates tasks: discovery, cataloging, cleansing, transformation, deduplication

### Blueprints
Ready-made ETL templates to ingest data from multiple sources

### Why Setup Data Lake?
- Centralized fine-grained access
- Don't have to manage security at multiple data sources
- Centralized permissions

## Amazon Managed Service for Apache Flink

### Overview
- More powerful than KDF
- Flink is used for processing and analytics
- KDF is used for loading and transformation of data to S3, Redshift, OpenSearch, New Relic, Datadog, Splunk, etc.
- **Data Source:** KDS, Kafka
- Cannot read data from Firehose
- **Deployment:** Provisioned and Serverless

## Amazon MSK (Managed Streaming for Apache Kafka)

### Overview
- Kafka is alternative to Kinesis, both allow you to stream data
- MSK allows you to create, update, delete cluster on the fly
- Deploy cluster in VPC across multi-AZ
- **Deployment:** Provisioned and MSK Serverless

### Kafka Architecture
1. **Producer:** Publishes messages to Kafka topic
2. **Consumer:** Subscribes to topic and processes messages
3. **Brokers:** Kafka runs as a cluster of brokers, each handling portion of data
4. **Topics:** Categories to which records are sent by producer
5. **Partitions:** Each topic is split into partitions, allowing parallelism and scalability
6. **Zookeeper:** Manages Kafka cluster, handling coordination, configuration, leader selection
7. **Replication:** Kafka replicates partitions across multiple brokers for fault tolerance

### Amazon MSK vs Kinesis Data Streams
**MSK:**
- Scales by partitions
- Long-term retention

**KDS:**
- Scales by shards
- 1 MB fixed message size
- Retention 365 days

### Who Consumes from MSK?
- Flink
- Custom Kafka clients
- Glue Streaming ETL
- Lambda triggers

## Big Data Ingestion Pipeline Examples

### IoT Core
Helps you manage IoT devices

**Example-1:**
IoT Core -> KDS -> KDF -> Every 1 min -> Lambda (transformation) -> S3 -> Lambda/SQS -> Athena

### Visualization
- Use QuickSight directly on S3 to generate reports
- Can also load data from S3 to Redshift for advanced analytics
