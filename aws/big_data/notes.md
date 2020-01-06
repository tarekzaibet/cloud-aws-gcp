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
