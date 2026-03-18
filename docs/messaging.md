# AWS Integration & Messaging


## Section Introduction

- When we start deploying multiple applications, they will inevitably need
    - to communicate with one another
- There are two patterns of application communication
    - 1) Synchronous communications 2) Asynchronous / Event based
(application to application) (application to queue to application)
Buying Shipping Buying Shipping
Queue
Service Service Service Service


## Section Introduction

- Synchronous between applications can be problematic if there are
    - sudden spikes of traffic
- What if you need to suddenly encode 1000 videos but usually it’s 10?
- In that case, it’s better to decouple your applications,
- using SQS: queue model
- using SNS: pub/sub model
- using Kinesis: real-time streaming model
- These services can scale independently from our application!


## Amazon SQS

What’s a queue?
Consumer
Producer
Consumer
Send messages
Producer Poll messages
Consumer
SQS Queue
Producer
Consumer


## Amazon SQS – Standard Queue

- Oldest offering (over 10 years old)
- Fully managed service, used to decouple applications
- Attributes:
- Unlimited throughput, unlimited number of messages in queue
- Default retention of messages: 4 days, maximum of 14 days
- Low latency (<10 ms on publish and receive)
- Limitation of 1,024 KB per message sent
- Can have duplicate messages (at least once delivery, occasionally)
- Can have out of order messages (best effort ordering)


## SQS – Producing Messages

- Produced to SQS using the SDK (SendMessage API)
- The message is persisted in SQS until a consumer deletes it
- Message retention: default 4 days, up to 14 days
- Example: send an order to be processed
- Order id
    - Sent to SQS
- Customer id
- Any attributes you want
    - Message
Up to 1024 KB
- SQS standard: unlimited throughput


## SQS – Consuming Messages

- Consumers (running on EC2 instances, servers, or AWS Lambda)…
- Poll SQS for messages (receive up to 10 messages at a time)
- Process the messages (example: insert the message into an RDS database)
- Delete the messages using the DeleteMessage API
    - Poll / Receive
messages
insert
Consumer
DeleteMessage


## SQS – Multiple EC2 Instances Consumers

- Consumers receive and process
    - messages in parallel
- At least once delivery
    - SQS Queue • Best-effort message ordering
- Consumers delete messages
    - after processing them
- We can scale consumers
    - horizontally to improve
throughput of processing
poll


## SQS with Auto Scaling Group (ASG)

Poll for messages
EC2 Instances
SQS Queue
Auto Scaling Group
scale
Alarm for breach
CloudWatch Metric – Queue Length CloudWatch Alarm
ApproximateNumberOfMessages


## SQS to decouple between application tiers

Back-end processing
Front-end web app
application
requests SendMessage ReceiveMessages
SQS Queue
(infinitely scalable)
Auto-Scaling
Auto-Scaling


## Amazon SQS - Security

- Encryption:
- In-flight encryption using HTTPS API
- At-rest encryption using KMS keys
- Client-side encryption if the client wants to perform encryption/decryption itself
- Access Controls: IAM policies to regulate access to the SQS API
- SQS Access Policies (similar to S3 bucket policies)
- Useful for cross-account access to SQS queues
- Useful for allowing other services (SNS, S3…) to write to an SQS queue


## SQS – Message Visibility Timeout

- After a message is polled by a consumer, it becomes invisible to other consumers
- By default, the “message visibility timeout” is 30 seconds
- That means the message has 30 seconds to be processed
- After the message visibility timeout is over, the message is “visible” in SQS
    - ReceiveMessage ReceiveMessage ReceiveMessage ReceiveMessage
Request Request Request Request
Visibility timeout
Not returned Not returned
Time
Message returned Message returned (again)


## SQS – Message Visibility Timeout

ReceiveMessage ReceiveMessage ReceiveMessage ReceiveMessage
Request Request Request Request
Visibility timeout
Not returned Not returned
Time
Message returned Message returned (again)
- If a message is not processed within the visibility timeout, it will be processed twice
- A consumer could call the ChangeMessageVisibility API to get more time
- If visibility timeout is high (hours), and consumer crashes, re-processing will take time
- If visibility timeout is too low (seconds), we may get duplicates


## Amazon SQS - Long Polling

message
- When a consumer requests messages from the
    - queue, it can optionally “wait” for messages to
arrive if there are none in the queue
- This is called Long Polling
- LongPolling decreases the number of API calls
    - made to SQS while increasing the efficiency and
SQS Queue
reducing latency of your application
- The wait time can be between 1 sec to 20 sec
    - (20 sec preferable)
- Long Polling is preferable to Short Polling
    - poll
- Long polling can be enabled at the queue level
    - or at the API level using WaitTimeSeconds
Consumer


## Amazon SQS – FIFO Queue

- FIFO = First In First Out (ordering of messages in the queue)
    - Send messages Poll messages
Producer Consumer
4 3 2 1 4 3 2 1
- Limited throughput: 300 msg/s without batching, 3000 msg/s with
- Exactly-once send capability (by removing duplicates using Deduplication ID)
- Messages are processed in order by the consumer
- Ordering by Message Group ID (all messages in the same group are ordered)
    - – mandatory parameter


## SQS with Auto Scaling Group (ASG)

Poll for messages
EC2 Instances
SQS Queue
Auto Scaling Group
scale
Alarm for breach
CloudWatch Metric – Queue Length CloudWatch Alarm
ApproximateNumberOfMessages


## If the load is too big,

some transactions may be lost
Amazon RDS
Application
Insert
transactions
requests
Amazon Aurora
Amazon DynamoDB
Auto-Scaling


## SQS as a buffer to database writes

Dequeue message
Enqueue message
requests SendMessage ReceiveMessages insert
SQS Queue
(infinitely scalable)
Auto-Scaling
Auto-Scaling


## SQS to decouple between application tiers

Back-end processing
Front-end web app
application
requests SendMessage ReceiveMessages
SQS Queue
(infinitely scalable)
Auto-Scaling
Auto-Scaling


## Amazon SNS

- What if you want to send one message to many receivers?
    - Direct Pub / Sub
Email
Email
integration
notification
notification
Fraud
Fraud
Service
Service
Buying Buying
Service Service
Shipping
Shipping
Service
SNS Topic Service
SQS Queue
SQS Queue


## Amazon SNS

- The “event producer” only sends message to one SNS topic
- As many “event receivers” (subscriptions) as we want to listen to the SNS topic notifications
- Each subscriber to the topic will get all the messages (note: new feature to filter messages)
- Up to 12,500,000 subscriptions per topic
- 100,000 topics limit
    - Subscribers
publish SQS Lambda Kinesis Data
Firehose
SNS
Emails SMS & HTTP(S)
Mobile Notifications Endpoints


## SNS integrates with a lot of AWS services

- Many AWS services can send data directly to SNS for notifications
    - …
CloudWatch Alarms AWS Budgets Lambda
…
publish
Auto Scaling Group S3 Bucket DynamoDB
(Notifications) (Events)
SNS
…
CloudFormation AWS DMS RDS Events
(State Changes) (New Replic)


## Amazon SNS – How to publish

- Topic Publish (using the SDK)
- Create a topic
- Create a subscription (or many)
- Publish to the topic
- Direct Publish (for mobile apps SDK)
- Create a platform application
- Create a platform endpoint
- Publish to the platform endpoint
- Works with Google GCM, Apple APNS, Amazon ADM…


## Amazon SNS – Security

- Encryption:
- In-flight encryption using HTTPS API
- At-rest encryption using KMS keys
- Client-side encryption if the client wants to perform encryption/decryption itself
- Access Controls: IAM policies to regulate access to the SNS API
- SNS Access Policies (similar to S3 bucket policies)
- Useful for cross-account access to SNS topics
- Useful for allowing other services ( S3…) to write to an SNS topic


## SNS + SQS: Fan Out

SQS Queue
Fraud
Service
Buying
Service
Shipping
SNS Topic Service
SQS Queue
- Push once in SNS, receive in all SQS queues that are subscribers
- Fully decoupled, no data loss
- SQS allows for: data persistence, delayed processing and retries of work
- Ability to add more SQS subscribers over time
- Make sure your SQS queue access policy allows for SNS to write
- Cross-Region Delivery: works with SQS Queues in other regions


## Application: S3 Events to multiple queues

- For the same combination of: event type (e.g. object create) and prefix
    - (e.g. images/) you can only have one S3 Event rule
- If you want to send the same S3 event to many SQS queues, use fan-out
    - SQS Queues
Fan-out
events
S3 Object
created…
SNS Topic
Amazon S3
Lambda Function


## Application: SNS to Amazon S3 through

Kinesis Data Firehose
- SNS can send to Kinesis and therefore we can have the following
    - solutions architecture:
Buying
Amazon S3
Service
SNS Topic Kinesis Data
Firehose
Any supported KDF
Destination


## Amazon SNS – FIFO Topic

- FIFO = First In First Out (ordering of messages in the topic)
    - Receive messages
Send messages
Subscribers
Producer
SQS FIFO
4 3 2 1 4 3 2 1
- Similar features as SQS FIFO:
- Ordering by Message Group ID (all messages in the same group are ordered)
- Deduplication using a Deduplication ID or Content Based Deduplication
- Can have SQS Standard and FIFO queues as subscribers
- Limited throughput (same throughput as SQS FIFO)


## SNS FIFO + SQS FIFO: Fan Out

- In case you need fan out + ordering + deduplication
    - SQS FIFO Queue
Fraud
Service
Buying
Service
Shipping
SNS FIFO Topic Service
SQS FIFO Queue


## SNS – Message Filtering

- JSON policy used to filter messages sent to SNS topic’s subscriptions
- If a subscription doesn’t have a filter policy, it receives every message
    - Filter Policy
SQS Queue
State: Placed
(Placed orders)
SQS Queue
(Cancelled orders)
Filter Policy
New transaction Email Subscription
Buying State: Cancelled
(Cancelled orders)
Service
Order: 1036
Filter Policy
Product: Pencil SNS Topic
State: Declined
Qty: 4 SQS Queue
State: Placed
(Declined orders)
SQS Queue
(All)


## Amazon Kinesis Data Streams

- Collect and store streaming data in real-time
    - Real-time data
Consumers
Application
Amazon Kinesis
Click Streams
Producers Data Streams
Lambda
Applications
Amazon
IoT devices
Data Firehose
Kinesis Agent
Managed
Metrics & Logs Service for
Apache Flink


## Kinesis Data Streams

- Retention between up to 365 days
- Ability to reprocess (replay) data by consumers
- Data can’t be deleted from Kinesis (until it expires)
- Data up to 10MiB (typical use case is lot of “small” real-time data)
- Data ordering guarantee for data with the same “Partition ID”
- At-rest KMS encryption, in-flight HTTPS encryption
- Kinesis Producer Library (KPL) to write an optimized producer application
- Kinesis Client Library (KCL) to write an optimized consumer application


## Kinesis Data Streams – Capacity Modes

- Provisioned mode:
- Choose number of shards
- Each shard gets 1MB/s in (or 1000 records per second)
- Each shard gets 2MB/s out
- Scale manually to increase or decrease the number of shards
- You pay per shard provisioned per hour
- On-demand mode:
- No need to provision or manage the capacity
- Default capacity provisioned (4 MB/s in or 4000 records per second)
- Scales automatically based on observed throughput peak during the last 30 days
- Pay per stream per hour & data in/out per GB


## Amazon Data Firehose

3rd-party Partner Destinations
Lambda
function
Datadog
Applications
Data
Kinesis
transformation
Data Streams AWS Destinations
Amazon S3
Client Record
Up to 1 MB
Amazon Redshift
Batch writes
Amazon
SDK
CloudWatch
Amazon
(Logs & Events)
Amazon OpenSearch
Data Firehose
All or Failed data
Kinesis Agent
Custom Destinations
AWS IoT
S3 backup bucket
Producers HTTP Endpoint


## Amazon Data Firehose

- Note: used to be called “Kinesis Data Firehose”
- Fully Managed Service
- Amazon Redshift / Amazon S3 / Amazon OpenSearch Service
- 3rd party: Splunk / MongoDB / Datadog / NewRelic / …
- Custom HTTP Endpoint
- Automatic scaling, serverless, pay for what you use
- Near Real-Time with buffering capability based on size / time
- Supports CSV, JSON, Parquet, Avro, Raw Text, Binary data
- Conversions to Parquet / ORC, compressions with gzip / snappy
- Custom data transformations using AWS Lambda (ex: CSV to JSON)


## Kinesis Data Streams vs Amazon Data Firehose

Kinesis Data Streams Amazon Data Firehose
- Streaming data collection • Load streaming data into S3 / Redshift /
    - OpenSearch / 3rd party / custom HTTP
- Producer & Consumer code
- Fully managed
- Real-time
- Near real-time
- Provisioned / On-Demand mode
- Automatic scaling
- Data storage up to 365 days
- No data storage
- Replay Capability
- Doesn’t support replay capability


## SQS vs SNS vs Kinesis

SQS: SNS: Kinesis:
- Push data to many
- Consumer “pull data” • Standard: pull data
    - subscribers
- 2 MB per shard
- Data is deleted after being
- Up to 12,500,000 subscribers
    - consumed • Enhanced-fan out: push data
- Data is not persisted (lost if
- 2 MB per shard per consumer
- Can have as many workers not delivered)
    - (consumers) as we want • Possibility to replay data
- Pub/Sub
- No need to provision • Meant for real-time big data,
- Up to 100,000 topics
    - throughput analytics and ETL
- No need to provision
- Ordering guarantees only on throughput • Ordering at the shard level
    - FIFO queues
- Integrates with SQS for fan-
- Data expires after X days
    - out architecture pattern
- Individual message delay
- Provisioned mode or on-
    - capability • FIFO capability for SQS FIFO
demand capacity mode


## Amazon MQ

- SQS, SNS are “cloud-native” services: proprietary protocols from AWS
- Traditional applications running from on-premises may use open protocols
    - such as: MQTT, AMQP, STOMP, Openwire, WSS
- When migrating to the cloud, instead of re-engineering the application to use
    - SQS and SNS, we can use Amazon MQ
- Amazon MQ is a managed message broker service for
- Amazon MQ doesn’t “scale” as much as SQS / SNS
- Amazon MQ runs on servers, can run in Multi-AZ with failover
- Amazon MQ has both queue feature (~SQS) and topic features (~SNS)


## Amazon MQ – High Availability

Region
(us-east-1)
ACTIVE Availability Zone
(us-east-1a)
Amazon MQ Broker
Amazon EFS
(storage)
Client STANDBY Availability Zone
failover (us-east-1b)
Amazon MQ Broker
