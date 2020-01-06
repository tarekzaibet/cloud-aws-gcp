# 1-Collection

- Real Time :
              - Kinesis Data Streams (KDS)
              - Simple Queue Service (SQS)
              - Internet of Things (IoT)
- Near Real Time :
              - Kinesis Data Firehose
              - Database Migration service (DMS)
- Batch - Historical Analysis :
              - Snowball
              - Data Pipeline

## AWS Kinesis  

- managed alternative to Apache Kafka
- suitable for Real Time & for application logs, metrics, IoT, clickstreams
- good for streaming processing frameworks
- data is automatically replicated in 3 AZ

- streams : low latency streaming ingest at scale
- firehose : load streams into s3, Redshift, elasticsearch & splunk
- analytics : perform real time analytics on streams using SQL

### Kinesis Streams
- streams are divided into shards / partitions
- data retention is 24 hours and can go up to 7 days
- ability to reprocess / replay data
- multiple applications can consume the same stream
- real time processing with scale of throughput
- once data is inserted in kinesis, it can't be deleted (immutable)

#### Shards
- One stream is made of many different shards
- Billing is per shard provisioned, can have as many shards as you want
- Batching available or per message calls
- The number of shards can evolve over time
- Records are ordered per shard

#### Records
- consist of : data blob, record key, sequence number.
- data blob : data being sent, 1MB max, can represent anything, sent in bytes
- record key : sent to group record in shards, same key = same shard, better to use highly distributed key to avoid the "hot partition" problem
- sequence number : unique identifier for each record put in shards.

#### Kinesis Data Streams Limits
- Producer :
  - 1 MB/s or 1000 messages/s at write PER SHARD
  - "ProvisionedThroughputException" otherwise

- Consumer Classic :
  - 2MB/s at read PER SHARD across all consumers
  - 5 API calls per second PER SHARD across all consumers

- Consumer Enhanced Fan-Out :
  - 2MB/s at read PER SHARD, PER ENHANCED CONSUMER
  - No API calls needed (push model)

- Data Retention :
  - 24 hours data retention by default

#### Kinesis Producers

How to produce data with kinesis.
Different ways :
    - Kinesis Producer SDK - PutRecord(s)
    - Kinesis Producer Library (KPL)
    - Kinesis Agent
    - 3rd Party libraries: Spark, Log4j, Kafka

- Kinesis Producer SDK - PutRecord(s)
  - APIs that are used are PutRecord (one) and PutRecords (many records)
  - PutRecords uses batching and increases throughput => less HTTP requests
  - ProvisionedThroughputException if we go over the limits
  - Use case : low throughput, higher latency, simple API, AWS Lambda
  - Managed AWS sources for Kinesis Data Streams : CloudWatch Logs, AWS IoT, Kinesis Data Analytics
  - Exception that can happen : ProvisionedThroughputException happens when :
    - exceeding MB/s or TPS for any shard
    - Make sure you don't have a hot shard (such as your partition key is bad and too much data goes to that partition)

    Solution:
    - Retries with backoff
    - Increase shards (scaling)
    - Ensure your partition key is a good one 
