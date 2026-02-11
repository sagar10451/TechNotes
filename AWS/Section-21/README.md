# AWS Database Services Comparison

## Amazon RDS
- Only choose RDS if you need to work with engines apart from PostgreSQL and MySQL
- Cheaper option

## Amazon Aurora
- Only supports PostgreSQL and MySQL
- In every aspect this is better than RDS
- Cost is more

## Amazon ElastiCache
- You have to provision (not the case with DAX)
- Requires code changes
- Used for storing session data

## Amazon DynamoDB
- Serverless
- Can store session data with TTL column feature
- Key-value pair storage

## Amazon S3
- Already studied

## Amazon DocumentDB
- Aurora version of MongoDB
- Stores JSON documents

## Amazon Neptune
- Knowledge graph database
- Use cases: social media, Wikipedia, fraud detection, recommendations
- Graph database
- **Neptune Streams:** Captures real-time changes in graph, produces ordered stream

## Amazon Keyspaces
- For Apache Cassandra

## Amazon Timestream
- For time-stamped data like IoT
- Time series data
- SQL capability
- Integration with: QuickSight, SageMaker, Grafana, JDBC, etc.
