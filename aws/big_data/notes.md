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


## AWS SQS - Standard Queue

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

## AWS SQS - FIFO Queue

- Newer offering (first in first out)
- Name of the Q must end in .fifo  
- Lower throughput (up to 3000 per second with batching, 300/s without)
- Messages are processed in order by the consumer
- Messages are sent exactly once
- 5-minute interval de-duplication using "Duplication ID"

## Kinesis Data Streams vs SQS

### Kinesis Data Streams :

- Data can be consumed many times
- Data is deleted after the retention period
- Ordering of records are preserved (at the shard level) - even during replays
- Build multiple applications reading from the same stream independently (Pub/Sub)
- "Streaming MapReduce" querying capability
- Checkpointing needed to track progress of consumption
- Shards (capacity) must be provided ahead of time

### SQS :

- Queue, decouple applications
- One application per Q
- Records are deleted after consumption (ack/fail)
- Messages are processed independently for standard Q
- Ordering for FIFO queues
- Capability to "delay" messages
- Dynamic scaling of load (no-ops)

## IoT

- We deploy IoT devices called "Things"
- We configure and retrieve data from these things
- The components of AWS IoT Cloud are :
  - IoT thing (device)
  - Thing Registry (basically like IAM for IoT)
  - Device Gateway (sends messages to IoT message broker and exchange informations with the IoT thing)
  - IoT Message Broker (sends messages to the IoT Rules Engine and exchange informations with the Device Shadow)
   - IoT Rules Engine (send informations to Kinesis, SQS, Lambda..)
   - Device Shadow (basically a replica of your device in AWS Cloud which allows your to continue development on your device when the real device in unavailable )

### IoT Components Deep Dive

#### IoT Device Gateway

- Serves as the entry point for IoT devices connecting to AWS
- Allows devices to securely and efficiently communicate with AWS IoT
- Supports the MQTT, WebSockets and HTTP 1.1 protocols
- Fully managed and scales automatically to support over a billion devices
- No need to manage any infrastructure

#### IoT Thing Registry (IAM of IoT)

- All connected IoT devices are represented in the AWS IoT registry
- Organizes the resources associated with each device in the AWS Cloud
- Each device gets a unique ID
- Supports metadata for each device
- Can create x.509 certificate to help IoT devices connect to AWS
- IoT Groups : group devices together and apply permissions to the group

#### IoT Message Broker

- Pub/Sub messaging pattern with low latency
- Devices can communicate with one another this way
- Messages sent using the MQTT, WebSockets or HTTP 1.1 protocols
- Messages are published into topics (just like SNS)
- Message Broker forwards messages to all clients connected to the topic

#### Rules Engine

- Rules are defined on the MQTT topics
- Rules = when it's triggered , Action = what it does
- Rules use cases :
  - Augment of filter data received from a device
  - Write data received from a device to DynamoDB database
  - Save a file to S3
  - Send a push notification to all users using SNS
  - Publish data to a SQS Queue
  - Invoke a lambda function to extract data
  - Process messages from a large number of devices using Amazon Kinesis
  - Send data to the Amazon Elasticsearch Service
  - Capture a CloudWatch metric and change a CloudWatch alarm
  - Send the data from an MQTT message to Amazon ML to make predictions based on an Amazon ML model
- Rules need IAM Roles to perform their actions


#### Device Shadow

- JSON document representing the state of a connected Thing
- We can set the state to a different desired date
- The IoT thing will retrieve the state when online and adapt

#### Authentication

- 3 possible authentication methods for things :
  - create x.509 certificates and load them securely onto things
  - AWS SigV4
  - Custom tokens with Custom authorizers

#### Authoization
- AWS IoT policies :
  - attached to x.509 certificates or Cognito identities
  - Able to revoke any device at any time
  - IoT Policies are JSON documents
  - Can be attached to groups instead of individual Things

- IAM Policies :
  - Attached to users, group or roles
  - Used for controlling IoT AWS APIs

#### IoT Greengrass
- brings the compute layer to the device directly
- you can execute AWS Lambda functions on the devices:
  - Pre-process the data
  - Execute predictions based on ML modes
  - Keep device data in sync
  - Communicate between local devices
- Operate offline
- Deploy function from the cloud directly to the devices

## Database Migration Service - DMS

- quick and secure way to migrate databases to AWS. it's resilient and self healing
- during the migration, the source database remains available
- supports homogenous (mysql to mysql) and heterogenous migrations (mysql to aurora)
- has continuous data replication using CDC (change data capture)
- you need to create an EC2 instance to perform replication tasks

### DMS Sources and Targets
#### Sources
- On-prem and EC2 instances db : Oracle, MySQL, MariaDB, SAP, DB2..
- Azure SQL DB
- Amazon RDS : including Aurora
- Amazon S3

#### Targets
- On-prem and EC2 instances db : Oracle, MySQL, MariaDB, SAP, DB2..
- RDS
- Redshift
- DynamoDB
- S3
- Elasticsearch
- Kinesis Data Streams
- DocumentDB

### AWS Schema Conversion Tool (SCT)

- Convert your DB Schema from one engine to another
- Example OLTP : (SQL server or Oracle) to MySQL, PostgreSQL, Aurora
- Example OLAP : (Teradata or Oracle) to Amazon Redshift
- You can use AWS SCT to create AWS DMS endpoints and tasks

## Direct Connect

- Provides a dedicated private connection from a remote network to your VPC
- Can setup multiple 1 Gbps or 10 Gbps dedicated network connections
- You need to setup a Virtual Private Gateway on your VPC
- Access public resources (S3) and private (EC2) on same connection
- Use Cases :
  - Increase bandwidth throughput - working with large data sets - lower costs
  - More consistent network experience - applications using real-time data feeds
  - Hybrid Environment (on prem + cloud)
  - Enhanced security (private connection)
- Supports both IPv4 and IPv6
- High-availability : two DC as failover or use Site-to-Site VPN as a failover

## Snowball

- Physical data transport solution that helps moving TBs or PBs of data in or out of AWS
- Alternative to moving data over the network(and paying network fees)
- Secure, tamper resistant, uses KMS 256 bit encryption
- Tracking using SNS and text messages, E-ink shipping label
- Pay per data transfer job
- Use cases : large data cloud migrations, DC decommission, disaster recovery
- If it take more than a week to transfer over the network, use Snowball devices

### Snowball Process
1 - Request snowball devices from the AWS console for delivery
2 - Install the snowball client on your servers
3 - Connect the snowball to your servers and copy files using the client
4 - Ship back the device when you're done (goes to the right AWS facility)
5 - Data will be loaded into an S3 bucket
6 - Snowball is completely wiped
7 - Tracking is done using SNS, text messages and the AWS console

### Snowball Edge
- Snowball Edges add computational capability to the devices
- 100 TB capacity with either :
  - storage optimized - 24 vCPU
  - compute optimized - 52 vCPU & optional GPU
- Supports a custom EC2 AMI so you can perform processing on the go
- Supports custom Lambda function
- Very useful to pre-process the data while moving
- Use case : data migration, image collation, IoT capture, machine learning

### AWS Snowmobile

- Best if you transfer more than 10 PB
- Each Snowmobile has 100 PB of capacity
- Transfer exabytes of data (1 EB = 1000 PB = 1000000 TBS)

# 2 - Storage

## S3

- stores objects in buckets
- buckets are unique globally
- buckets are at the region level

### Objects

- objects have a key. key = full path
- inside a bucket there is no concept of "directories"
- keys with slashes : bucket/folder/file
- object values = content of the body :
  - max object size is 5 TB
  - if size > 5GB, we need to use "multi-part upload"
- objects have metadata (list of text key / value paris - system or user metadata)
- tags (unicode key/value pair) useful for security and lifecycle
- version ID (if versioning enabled )

### Consistency model
- Read after write consistency for PUTS of new objects
- Eventual consistency for DELETES and PUTS of existing objects

### Storage Tiers

#### S3 - Standard - General Purpose
- High durability (11 9's) of objects across mulitple AZ
- 99,99% availability over a given year
- Sustain 2 concurrent facility failures
- use cases : big data analytics, mobile & gaming, content distribution

#### S3 - IA
- when data is less frequently accessed, but requires quick access
- high durability (11 9's) of objects across mulitple AZ
- 99,99% availability  
- low cost compared to S3 standard
- Sustain 2 concurrent facility failures  

#### S3 - One Zone - IA

- Same as IA but stored in one AZ
- high durability (11 9's) in a single AZ
- 99,95% availability
- supports SSL for data in transit and encryption at rest
- low cost compared to IA (by 20%)
- low latency and high throughput performance

#### S3 intelligent tiering

- same low latency and high throughput performance of S3 standard
- small monthly monitoring and auto-tiering fee
- automatically moves objects between two access tiers based on changing access patterns
- High durability (11 9's) of objects across mulitple AZ
- 99,99% availability over a given year
- Resilient against events that impact an AZ

#### S3 Glacier

- low cost object storage meant for archiving / backup
- data is retained for the long term (10s of years)
- alternative to on-premise magentic tape storage
- average annual durability is (11 9's)
- cost per storage per month (0.004 $/ GB) + retrival cost
- each item in glacier is called "Archive" (up to 40TB)
- Archives are stored in "Vaults"
- 3 retrival options :
  - expedited (1 to 5 minutes retrival)
  - standard (3 to 5 hours)
  - bulk (5 to 12 hours)

### S3 Lifecycle Rules

- Rules to move data between different tiers, to save storage cost
- example : Standard to IA to Glacier
- Transition actions : defines when objects are transitioned to another storage class.
- Expiration actions : configure objects to expire after a certain time period
- Move to glacier is good for backup, long term retention, regulatory needs

### S3 Cross Region Replication

- must enable versioning (source & destination)
- buckets must be in different AWS regions
- can be in different accounts
- copying is async
- must give right permissions to S3
- use cases : compliance, lower latency, replication across accounts

### S3 - Etag

- verify if a file is already uploaded to S3
- with this you make sure that the file is exactly the same
- for uploads < 5GB, it's a MD5 hash
- for multi-part its more complicated hash
- with Etag you ensure the integrity of files  

### S3 Performance

- When we had > 100 transaction per second (TPS), S3 performance cloud degrade
- what happens is that each object goes to an S3 partition and for the best performance, we want the highest partition distribution
- historically, it was recommended to have random characters in front of your key name to optimise performance
- not in the exam  !:
  - since july 2018 we can scale up to 3500 RPS for PUT and 5500 RPS for GET for each prefix
  - " this S3 request rate performance increase removes any previous guidance to randomize object prefixes to achieve faster performance"
- upload fastly large file using multipart upload for files > 5GB
- use cloudfront to cache S3 objects and to improve reads
- S3 transfer acceleration
- if using SSE-KMS encryption you may be limited to your AWS limits for KMS usage (100-1000s downloads / uploads per second )

### S3 Encryption for Objects

4 methods :
- SSE-S3 : encrypts S3 objects using keys handled & managed by AWS
- SSE-KMS : leverage AWS Key Management Service to manage encryption keys
- SSE-C : when you want to manage your own encryption keys
- Client Side Encryption

#### SSE-S3
- encryption using keys handled & managed by AWS S3
- object is encrypted server side
- AES-256 encryption type
- you must set a header for http/s request with "x-amz-server-side-encryption":"AES256"
- the key used is a S3 managed data key

#### SSE-KMS
- encryption using keys handled & managed by KMS
- KMS advantages : user control + audit trail
- object is encrypted server side
- we set a head "x-amz-server-side-encryption":"aws:kms"
- the key is a KMS Customer Master Key

#### SSE-C
- keys fully managed by the customer outside of AWS
- S3 does not store the encryption key you provide
- HTTPS must be used
- Encryption key must be provided in HTTPS header, for every HTTPS request made
- AWS does the encryption and than throws away the key
-  the key is a client provided data key

#### Client Side Encryption
- you encrypt data yourself before sending to S3
- you decrypt data yourself when retrieving from S3
- you can use client libraries such as amazon S3 encryption Client
- Customer fully manages the keys and encryption cycle

#### Encryption in transit (SSL)
- AWS S3 exposes :  
  - HTTP endpoint : non encrypted
  - HTTPS endpoint : encryption in flight
- you're free to use the endpoint you want, but HTTPS is recommended
- HTTPS in mandatory for SSE-C
- Encryption in flight is also called SSL/TLS

### S3 Security

#### S3 CORS
- if your request data from another website, you need to enable CORS
- CORS allows you to limit the number of websites that can request your files in S3 (and limit your costs)

#### S3 Access Logs
- you want to log all access to S3 buckets
- any request made to S3, from any account, authorized or denied, will be logged into another S3 bucket
- that data can be analyzed using data analysis tools
- or amazon athena

#### Overall Security
- User based :  
  - IAM policies - which API calls should be allowed for a speicifc user from IAM console
  - Resource Based :
    - Bucket policies : bucket wide rules from the S3 console
    - Objects ACL : finer grain  
    - Bucket ACL : less common

#### S3 Bucket Policies
- JSON based policies :
  - Resources : buckets and objects
  - Actions : set of API to Allow or Deny
  - Effect : Allow / Deny
  - Principal : the account or user to apply the policy to
- Use S3 bucket policy to :
  - Grant public access to the bucket
  - Force objects to be encrypted at upload
  - Grant access to another account (Cross Account)

#### S3 Default Encryption vs Bucket Policies
- the old method was to use a bucket policy and refuse any HTTP command with the correct headers
- the new way is to use the "default encryption" option in S3
- Note : Bucket policies are evaluated before "default encryption"

#### S3 Security Other
- Networking :
  - supports VPC endpoints
- Logging and audit :
  - S3 access logs can be stored in other S3 bucket
  - API calls can be logged in AWS CloudTrail
- User Security :
  - MFA
  - Signed URLs

#### Glacier - Vault Policies & Vault Lock  
- Vault = collection of archives
- Each vault has :
  - ONE vault access policy
  - ONE vault lock policy
- Vault Policies are written in JSON
- Vault Access Policy is similar to bucket policy (restrict user / account permission)
- Vault Lock Policy is a policy you lock, for regulatory and compliance requirements
   - the policy is immutable, it can never be changed (thats why is called lock)
   - example : forbid the delete of an archive if less than 2 years olds
   - example : implement WORM (write once read many)

## DynamoDB

- fully managed, high available nosql database with replication across 3 AZ
- scales to massive workloads, distributed database
- 100s of TB of storage
- Millions of requests per seconds and trillions of row
- fast and consistence in performance (low latency on retrieval)
- integrated with IAM for security, authorization and administration
- enables event driven programming with DynamoDB streams
- Low cost and auto scaling capabilities

- made of tables
- each table has a primary key (must be decided at creation time)
- each table can have an infinite number of items (=rows)
- each item has attributes
- maximum size of item is 400 KB
- Supported Data Types :  
  - Scaler : string, number, binary, bool, null
  - Document types : List, Map
  - Set Types : String Set, Number Set, Binary Set

- DynamoDB in big data
  - use cases : mobile apps, gaming, live voting, Log ingestion, web session management..
  - anti pattern :
    - larga data with low i/o use S3
    - BLOG data : store data in s3 & metdata in dynamodb
    - joins or complex transactions
    - app that uses RDS

### DynamoDB - Primary Keys
- Option 1 : Partition Key Only (HASH)
  - partition key must be unique for each item
  - partition key must be "diverse" so that the data is distributed
  - example : user_id for a users table

- Option 2 : Partition Key + Sort Key
  - The combination must be unique
  - Data is grouped by partition key
  - Sort key = range key
  - example : users-games table :
    - user_id for the partition key
    - game_id for the sort key

### DynamDB - RCU & WCU

- Table must have provisioned read and write capacity units
- Read Capacity Units (RCU) : throughput for reads
- Write Capacity Units (WCU) : throughput for writes
- Option to setup auto-scaling of throughput to meet demand
- Throughput can be exceeded temporarily using "burst credit"
- if burst credit are empty, you'll get a "ProvisionedThroughputException"
- it's then advised to do an exponential back-off retry

#### DynamoDB - WCU
- 1 WCU = 1 Write/second for an item up to 1KB
- if the items are larger than 1 KB, more  WCU are consumed
- example 1 : we write 10 objects per second of 2 KB
  we need 2 * 10 = 20 WCU
- example 2 : we write 6 objects per second of 4.5 KB
  we need 6 * 5 = 30 WCU (4.5 get rounded to upper KB)
- example 3 = we write 120 objects per minute of 2 KB each
  we need 120/60 * 2 = 4WCU
- number of objects per second * size of object

#### DynamoDB - RCU
- 1 RCU = 1 strongly consistent read per second or 2 eventually consistent reads per second, for an item up to 4 KB in size
- if the items are larger than 4 KB, more RCU are consumed
- example 1 :  10 strongly consistent reads per seconds of 4 KB each
  - we need 10 * 4 KB / 4 KB = 10 RCU
- example 2 : 16 eventually consistent reads per seconds of 12 KB each
  - we need (16 / 2) * (12 / 4) = 24 RCU
- example 3 : 10 strongly consistent reads per seconds of 6 KB each
  - we need 10 * 8 KB / 4 = 20 RCU
- RCU for consistent reads :
  - number of consistent reads * (size of object rounded up to mult(4) if needed / 4 )
- RCU for eventual reads :
  - ( number of consistent reads / 2 ) * (size of object rounded up to mult(4) if needed / 4)

#### DynamoDB - Throttling
- if we exceed our RCU or WCU we get "ProvisionedThroughputException"
- reasons :
  - hot keys/partitions : one partition key is being read too many times
  - very large items : remember RCU and WCU depends on size of items
- solutions :
  - exponential back-off when exception is encountered
  - distribute partition keys as much as possible
  - if RCU issue, we can use DynamoDB Accelerator (DAX)

### DynamoDB - Partitions
- you start with one partition
- each partition :
  - max of 3000 RCU / 1000 WCU
  - max of 10 GB
- to compute the number of partitions :
  - by capacity : (TOTAL RCU/3000) + (TOTAL WCU /1000)
  - by size : Total Size / 10 GB
  - Total partitions = CEILING(MAX(Capacity,Size))
- WCU and RCU are spread equally between partitions

### DynamoDB - API

#### Writing Data
- PutItem : writes data to dynamodb, by adding or full repalce
- UpdateItem : update data in dynamodb, partial updates or attributes
- Conditional Writes :
  - Accept write / update only if a condition is met
  - Helps with concurrent access to items
  - No performance impact

#### Deleting Data
- DeleteItem
  - Delete an individual row
  - Ability to perform a conditional delete
- DeleteTable
  - Delete a whole table and all its items
  - Much quicker deletion than calling DeleteItem on all items

#### Batching Writes
- BatchWriteItem :
  - Up to 25 PuttItem and/or DeleteItem in one call
  - Up to 16 MB of data written
  - Up to 400KB of data per item
- Batching allows you to save latency by reducing the number of API calls done against DynamoDB
- Operations are done in parallel for better efficiency
- It's possible for part of a batch to fail, in which case we have the try the failed items (using exponential back-off algorithm)

#### Reading Data
- GetItem :
  - read based on Primary key
  - primary key = HASH or HASH-RANGE
  - eventually consistent read by default
  - option to use strongly reads (more RCU - might take longer)
  - ProjectionExpression can be specified to include only certain attributes

- BatchGetItem :
  - Up to 100 items
  - Up to 16 MB of data
  - Items are retrieved in parallel to minimize latency

#### DynamoDB - Query
- Query returns items based on:
  - PartitionKey value (must be = operator)
  - SortKey - optional
  - FilterExpression to further filer (client side filtering)
- Returns :
  - Up to 1 MB of data
  - Or number of items specified in Limit
- Able to do pagination on the results
- Can query table, a local secondary index or a global secondary index

#### Scan
- Scan the entire table and then filter out data
- Returns up to 1 MB of data - use pagination to keep on reading
- Consumers a lot of RCU
- Limit impact using Limit or reduce the size of the result and pause 
- For faster performance, use parallel scans :  
  - Multiple instances scan multiple partitions at the same time
  - Increases the throughput and RCU consumed
  - Limit the impact of parallel scans just like you would for Scans
- Can use a ProjectionExpression + FilterExpression (no change to RCU)

### LSI & GSI 

#### Local Secondary Index (LSI)
- Alternate range key for your table, local to the hash key 
- Up yo five local secondary indexes per table 
- The sort key consists of exactly one scalar attribute 
- The attribute that you choose must be a scalar string, number of binary 
- LSI must be defined at table creation

#### Global Secondary Indes (GSI)

- Used to speed up queries on non-key attributes 
- Consists of partition key and optional sort key 
- The index created is a new "table" and we can project attributes on it 
  - the partition key and sort key of the original table are always projected (keys_only)
  - can specifiy extra attributes to project (INCLUDE)
  - can use all attributes from main table (ALL)
- we must define RCU / WCU for the index 
- we can modify GSI after table creation (not LSI)

### DynamoDB - DAX 

- it's dynamodb accelerator 
- secure (encryption at rest with KMS, VPC, IAM, CloudTrail..)
- Multi AZ (3 node minimum recommended for production)
- Up to 10 node in the cluster 
- 5 minutes TTL for cache by default 
- Micro second latency for cached reads & queries 
- Seamless cache for DynamoDB, no application re-write 

### DynamoDB - Streams 

- Changes in dynamo (create, update, delete) can be stored in a DynamoDB Stream 
- the stream can be read by aws lambda and then we can do : 
  - react to changes in real time (welcome message to new users)
  - create derivative tables / views 
  - insert into elasticsearch 
- can implement Cross Region Replication using Streams 
- Stream has 24 hours of data retention 
- Configurable batch size (up to 1000 rows, 6 MB)

#### DynamoDB Steams - Kinesis Adapter 

- you can use kinesis KCL to consumer from dynamodb streams 
- need to add "Kinesis Adapter" library 
- the interface and programming is exactly the same as kinesis streams 
- alternative to use aws lambda 

### DynamoDB TTL 

- automatically delete an item after an expiry date /time 
- provided with no extra cost, no WCU or RCU are used 
- a background task operated by dyanmodb itself 
- helps reduce storage and manage the table size over time 
- heps adhere to regulatory norms 
- ttl is enabled per row (you define a ttl column and add a date there)
- typically delete expired items with 46 hours of expiration 
- deleted items due to TTL are also deleted in GSI / LSI 
- Streams can help recover expired items 