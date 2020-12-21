# AWS Big Data Specilty
* Data Collection
* Data Storage
* Data Processing
* Data Analytics
* Data Visualization
* Data Security


## Collection
* Real Time - immediate actions
    * Kinesis Data Streams (KDS)
    * Simple Queue Service (SQS)
    * Internet of Things (IoT)
 
* Near-real time - reactive actions
    * Kinesis Data Firehose (KDF)
    * Database Migration Service (DMS)

* Batch - historical analysis (usually very large dataset)
    * Snowball
    * Data Pipeline

### 1. AWS Kinesis Data Stream (KDS)
* Kinesis is a managed alternative to Apache Kafka
* Great for "real-time" big data
* Great for application logs, metrics, IoT, clickstreams
* Great for stream processing framework (Spark, NiFi, etc...)
* Great for automatically replicated synchronously to 3 AZ

* KDS: low latency streaming ingest at scale
* KDF: load streams into S3, Redshift, ElasticSearch, Splunk, etc...

1.1 Kinesis
* Input sources (click streams, IoT devices, Metic and logs)
* KDS -> Kinesis Analytics -> KDF
* Output source (S3, Redshift)

1.2 Kinesis Data Streams
* Streams are divided in ordered Shards
* Data retention in 24 hours by default, can go up to 7 days.
* Ability to reprocess / replay data
* Multiple applications can consume the same stream
* Real-time processing with scale of throughput
* KDS is not database. Once data is inserted in KDS, it can't be deleted (immutability). So it is append only steam.

1.3 KDS Shards
* One stream is made of many different shards
* The number of shards can be changed by reshard or merge
* Records are ordered only per shard, not globally.
* Batching available or per message calls.

1.4 KDS Records
* Data blob: serialized as bytes. Up to 1 MB.
* Record key: same key = same shard. It should use a highly distributed key to avoid hot partition problem
* Sequence number: Added by Kinesis after ingestion. Unique identifier for each record put in shards.

1.5 KDS Limits
* Producer:
    * Per shard: 1MB/s or 1000 records at write
    * "ProvisionedThroughputException" otherwise
* Consumer classic:
    * Across all consumers: 2MB/s at read per shard
    * Across all consumers: 5 API calls per shard
* Consumer Enhanced Fan-Out:
    * Per enhanded consumer: 2MB/s at read per shard
    * No API calls needed (push model)
* Data retention:
    * 24 hours by default, can go up to 7 days.

1.6 Kinesis Producers
* Kinesis SDK:
    * Synchronous APIs that ared used: PutRecord and PutRecords
    * PutRecords uses batching and increasing throughput => less HTTP requests
    * ProvisionedThroughputExceeded if we go over the limits (exceeding MB/s or TPS per shard, hot partition shard)
        * Solution 1: Retries with backoff
        * Solution 2: Increase shards (scaling)
        * Solution 3: Ensure your partition key is a good one
    * AWS mobile sdk, android, ios, etc...
    * Use case: low throughput, high latency, simple API, aws lambda
    * Managed AWS sources for Kinesis Data Streams:
        * CloudWatch logs
        * AWS IoT
        * Kinesis Data Analytics (can be a producer to KDS)
* Kinesis Producer Library
    * **Synchronous or Asynchronous API** (better performance for async)
    * Used for building high performance, long-running produces
    * Automated and configurable retry mechanism
    * Easy to use and highly configurable C++ / Java library
    * Submitting metrics to CloudWatch for mornitoring
    * **Batching** (both turned on by default) - increase throughput, decrease cost:
        * Collect records and write to multiple shards in the same PutRecords API call
        * Aggregate - increase latency: 
            * Capability to store multiple records in one record, so it can go over the 1000 records per second limit
            * Increase payload size and improve throughput ( max 1MB/s limit)
    * **Batching example**: add latency to increase throughput
        * aggregation rt1 (r1 2kb, r2 40kb, r3 500kb)
        * aggregation rt2 (r4 1kb, r5 30kb, r6 80kb, r7 200kb)
        * Collection (PutRecords rt1 and rt2) and send out as one recod rt. rt contains 7 original records with a total size of 853kb (still less than 1MB)
        * batching efficiency: RecordMaxBufferedTime(default 100ms)
    * Compression: must be implemented by user
    * KPL records must be de-coded with KCL or special helper library
* Kinesis Agent: just need a configuration to setup 
    * Mornitor log files and send them to Kinesis
    * Java based agent, build on top of KPL
    * Only install in linux environments for now
    * features:
        * Write from multiple directories and write to multiple steams
        * Routing feature based on directory / log file
        * Pre-process data before sending to the streams(single line, csv to json, log to json ...)
        * The agent handles file rotation, checkpointing, and retry upon failures
        * Emits metrics to CloudWatch for moornitoring
* 3rd parth library: spark, Log4j, Appenders, Flume, Kafka Connect, NiFi...

1.7 Kinesis Consumers
* Kinesis SDK
    * Consumers pull data from a shard
    * Each shard has 2MB/s total aggregate throughput
    * GetRecords return up to 10MB of data (then throttle for 5 seconds) or up to 10,000 records
    * Max of 5 GetRecords API calls per shard per second = 200ms latency
    * If 5 consumers pull data from the same shard, means every consumer can poll once / per second and receives less than 400KB/s
* Kinesis Client Library (KCL)
    * Java / Golang / Python / Ruby / Node / .NET
    * Read records from Kinesis produced with KPL (de-aggregation)
    * Share multiple shards with multiple consumers in one group, shard discovery
    * Checkpointing feature to resume progress 
    * Leverage DynamoDB for coordination and checkpointing (one row per shard)
        * Make sure you provision enough WCU/RCU or use on-demand for DynamoDB
        * **Otherwise DynamoDB may slow down KCL on checkpointing**
    * Record processors will process the data
* Kinesis Connector Library 
    * Old Java library, 2016, leverage on KCL library
    * Must run on EC2
    * Write data to
        * S3
        * DynamoDB
        * Redshift
        * ElasticSearch
    * It is replaced by Kinesis Firehose and Lambda
* AWS Lambda:
    * Can source records from Kinesis Data Streams
    * Lambda consumer has a library to de-aggregate record from the KPL
    * Lambda can be used to run lightweight ETL to :
        * S3
        * DynamoDB
        * Redshift
        * ElasticSearch
        * Anywhere you want
    * Can be used to trigger notification / send emails
    * Lambda has a configurable batch size ( more in lambda section)
* Kinesis Firehose
* Kinesis Consumer Enhanced Fan-Out
* 3rd party libraries: spark, log4j, appenders, flume, kafka, connect...


#### Collection Tools:
| |Producer|Consumer|collection system (order, data structure, metadata)| durability | availablity|security|
|--|--|--|--|--|--|--|
|IoT|1.IoT device 2.Cli and SDK 3.AWS services|S3, DynamoDB, etc. | | | |1. X.509 2.Cognito 3. IAM|
|Kinesis Data Streaming|1.IOT 2.Kinesis Agent 3.Kinesis Producer Library 4.AWS SDKs|1.Data Ayalytics 2.Kinesis consumer lib 3.AWS SDKs|
|Kinesis Firehose Streaming|1.IOT 2.Kinesis Agent 3.Kinesis Producer Library 4.AWS SDKs|1.Other AWS service(s3,redshift,dynamodb,emr) 2.Same as KDS|
|Database Migration Service|database|database in AWS|
|Greengrass|local devices|AWS service|
|Snowball|on premises|AWS storage|
|Direct Connect|on premises|AWS service|



Sample questions:
1. 5 clients will take 80% of Redshift resources, other small clients take 20%. How to handle this case?
2. KDS consumed 2 million records, but lambda only processes 450k records and not throttleing problem. What is the potential test?
3. Lots of small S3 files and found EMA slows down. What is the potential solution? Aggregate small files to big file?
4. which api makes sure that the message will be delivered? .add() or putItem().
5. Create a user lookup system. Each user only have the right to access its own record. All users already have amazon account. How to design?
  * Use amazon account to get a federate token. Create a dynamoDB table and use user_id as partition key. Create a IAM policy with leading head and read access only.
  
6. How to sync EMRFS and EMR to make sure EMR always read the latest data from S3?
7. Kerberos
8. Big data tool for quering PB data. Pig?
9. ML Classification: spam emails that needs to be contact or ignore. What is target attributes?
10. Company opens anther market in other country. New country requires regulation on all past 24 transactions. Cost effective solution is to use KDS to export data to other region?
11. Customer wants to query data but still uses JDBC connection. Use presto to open JDBC connection?
12. Company has thousands of shipments. Each client may have one or more orders. How to allow each client to trace its own order, view and receive notification.
13. Segment
14. EC2 types for machine learning, computing etc.
15. EMR spark is used to process streaming data. Company wants to keep the original raw data. Put KFS in front of the spark?
16. Redshift EVEN distribute
17. Redshift leaf??
18. IAM API keys.
19. Kinesis cost: random_cut_forest
