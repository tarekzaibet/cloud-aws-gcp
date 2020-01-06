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

- Kinesis Producer Library (KPL)
  - C++ / Java library
  - Used for building high performance, long-running producers
  - Automated and configurable retry mechanism
  - Synchronous or Asynchronous API (better performance for async)
  - Submits metrics to CloudWatch for monitoring
  - Compression must be implemented by the user
  - KPL Records must be de-coded with KCL or special helper library
  - Batching (both turned on by default) - increase throughput, decrease cost:
      - Collect Records and Write to multiple shards in the same PutRecords API Call
      - Aggregate - increased latency :
        - Capability to store multiple records in one record (go over 1000 records per second limit)
        - Increase payload size and improve throughput (maximize 1MB/s limit)

      - example :
         1 - receive a 2 KB record
         2 - KPL waits a bit
         3 - receive  a 40 KB record
         4 - receive  a 500 KB record
         5 - KPL aggregates all into one record < 1MB
         6 - KPL does this again on other received records
         7 - now KPL has to send multiple PutRecords but no it will send all
         in one time using PutRecords - collection (1 PUT multiple records)

         now we have aggregation and collection

        - how kinesis know how much to wait in order to batch these records ?
           by setting RecordMaxBufferedTime (default 100ms) 
