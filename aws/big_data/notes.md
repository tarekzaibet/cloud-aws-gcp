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

- Kinesis Agent :
    - Monitor Log files and sends them to kinesis data streams
    - Java-based agent, built on top of KPL
    - Install in Linux-based server environments

    - Features :
      - write from multiple directories and write to multiple streams
      - routing feature based on directory / log file
      - pre-process data before sending to streams (single line, csv to json, log to json)
      - the agent handles file rotation, checkpointing and retry upon failures
      - Emits metrics to CloudWatch for monitoring

#### Kinesis Consumers - Classic

- Kinesis SDK
- Kinesis Client Library (KCL)
- Kinesis Connector Library
- 3rd party libraries : spark, log4j..
- Kinesis Firehose
- AWS Lambda

#### Kinesis Consumer Enhanced Fan-Out

      - Kinesis Consumer SDK - GetRecords :
        - Classic Kinesis - Records are polled by consumers from a shard
        - Each shard has 2 MB total aggregate throughput
        - GetRecords returns up to 10 MB of data (then throttle for 5 seconds) or up to 10000 records
        - Maximum of 5 GetRecords API calls per shard per second = 200ms latency
        - if 5 consumers application consume from the same shard, means every consumer can poll once a second a receive less than 400 KB/s

      - Kinesis Client Library (KCL):
        - Java-first library put exists for other languages (golang, python, ruby, node)
        - Read records from kinesis produced with the KPL (de-aggregation)
        - Share multiple shards with multiple consumers in one "group", shard discovery
        - Checkpointing feature to resume progress (go back where it stopped)
        - Leverages DynamoDB for coordination and checkpointing (one row per shard)
          - make sure you provision enough WCU / RCU
          - or use on-demand for DynamoDB
          - Otherwise DynamoDB may slow down KCL
        - Record processors will process data

#### Kinesis Scaling

- Adding Shards :
  - Also called "shard splitting"
  - Can be used to increase the Stream capacity (1MB/s data in per shard)
  - Can be used to divide a "hot shard"
  - The old shard is closed and will be deleted once the data is expired

- Merging Shards :
  - Decrease the Stream capacity and save costs
  - Can be used to group two shards with low traffic
  - Old shards are closed and deleted based on data expiration

- Kinesis Scaling Limitations :
  - Resharding cannot be done in parallel. Plan capacity in advance
  - You can only perform one resharding operation at a time and it takes a few seconds
  - For 1000 shards, it takes 30K seconds (8.3 hours) to double the shards to 2000.


### Kinesis Firehose

- Fully managed Service, no administration
- Near Real Time (60 seconds latency minimum for non full batches)
- Load data into : S3, Redshift, Elasticsearch, Splunk
- Automatic scaling
- Supports many data formats
- Data conversion from JSON to Parquet / ORC (only for s3)
- Data transformation through AWS Lambda (ex: CSV => JSON)
- Supports compression when target is Amazon S3 (GZIP, ZIP and SNAPPY)
- Only GZIP is the data further loaded into Redshift
- Pay for the amount of data going through Firehose
- Spark / KCL do not read from KDF


### AWS SQS - Standard Queue

- Oldest offering (over 10 years old)
- Fully managed
- Scales from 1 message per second to 10000s per second
- Default retention of messages : 4 days, maximum of 14 days   
- No limit to how many messages can be in the queue
- Low latency (<10 ms on publish and receive)
- Horizontal scaling in terms of number of consumers
- Can have duplicate messages (at least once delivery, occasionaly)
- Can have out of order messages (best effort ordering)
- Limitation of 256KB per message sent

### AWS SQS - FIFO Queue

- Newer offering (first in first out)
- Name of the Q must end in .fifo  
- Lower throughput (up to 3000 per second with batching, 300/s without)
- Messages are processed in order by the consumer
- Messages are sent exactly once
- 5-minute interval de-duplication using "Duplication ID"

### Kinesis Data Streams vs SQS

#### Kinesis Data Streams :

- Data can be consumed many times
- Data is deleted after the retention period 
- Ordering of records are preserved (at the shard level) - even during replays
- Build multiple applications reading from the same stream independently (Pub/Sub)
- "Streaming MapReduce" querying capability
- Checkpointing needed to track progress of consumption
- Shards (capacity) must be provided ahead of time

#### SQS :

- Queue, decouple applications
- One application per Q
- Records are deleted after consumption (ack/fail)
- Messages are processed independently for standard Q
- Ordering for FIFO queues
- Capability to "delay" messages
- Dynamic scaling of load (no-ops)
