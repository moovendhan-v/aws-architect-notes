# Data & Analytics


## Amazon Athena

- Serverless query service to analyze data stored in Amazon S3
- Uses standard SQL language to query the files (built on Presto)
    - load data
- Supports CSV, JSON, ORC, Avro, and Parquet
- Pricing: $5.00 per TB of data scanned
    - S3 Bucket
- Commonly used with Amazon Quicksight for
    - reporting/dashboards
Query & Analyze
Amazon
- Use cases: Business intelligence / analytics / reporting, analyze &
    - Athena
query VPC Flow Logs, ELB Logs, CloudTrail trails, etc...
- Exam Tip: analyze data in S3 using serverless SQL, use Athena Reporting & Dashboards
    - Amazon
QuickSight


## Amazon Athena – Performance Improvement

- Use columnar data for cost-savings (less scan)
- Apache Parquet or ORC is recommended
- Huge performance improvement
- Use Glue to convert your data to Parquet or ORC
- Compress data for smaller retrievals (bzip2, gzip, lz4, snappy, zlip, zstd…)
- Partition datasets in S3 for easy querying on virtual columns
- s3://yourBucket/pathToTable
    - /<PARTITION_COLUMN_NAME>=<VALUE>
/<PARTITION_COLUMN_NAME>=<VALUE>
/<PARTITION_COLUMN_NAME>=<VALUE>
/etc…
- Example: s3://athena-examples/flight/parquet/year=1991/month=1/day=1/
- Use larger files (> 128 MB) to minimize overhead


## Amazon Athena – Federated Quer y

Amazon
- Allows you to run SQL queries across
    - Athena
data stored in relational, non-relational,
S3 Bucket
object, and custom data sources (AWS
or on-premises) Lambda
(Data Source
ElastiCache Connector)
- Uses Data Source Connectors that run
    - on AWS Lambda to run Federated
Queries (e.g., CloudWatch Logs,
DocumentDB
HBase in EMR
DynamoDB, RDS, …)
- Store the results back in Amazon S3
    - DynamoDB
Database
(On-Premises)
Redshift
Aurora SQL Server MySQL


## Redshift Overview

- Redshift is based on PostgreSQL, but it’s not used for OLTP
- It’s OLAP – online analytical processing (analytics and data warehousing)
- 10x better performance than other data warehouses, scale to PBs of data
- Columnar storage of data (instead of row based) & parallel query engine
- Two modes: Provisioned cluster or Serverless cluster
- Has a SQL interface for performing the queries
- BI tools such as Amazon Quicksight or Tableau integrate with it
- vs Athena: faster queries / joins / aggregations thanks to indexes


## Redshift Cluster

- Leader node: for query
    - SELECT COUNT (*), …
Query FROM MY_TABLE planning, results
GROUP BY …
aggregation
JDBC/ODBC
- Compute node: for
    - performing the queries,
Amazon Redshift Cluster
send results to leader
Leader Node
- Provisioned mode:
- Choose instance types
    - in advance
Compute Nodes
- Can reserve instances
    - for cost savings


## Redshift – Snapshots & DR

Region
- Redshift has “Multi-AZ” mode for some clusters (us-east-1)
- Snapshots are point-in-time backups of a cluster,
    - stored internally in S3 Take Snapshot Cluster
Snapshot
- Snapshots are incremental (only what has
    - changed is saved)
Redshift Cluster
- You can restore a snapshot into a new cluster
    - (Original)
- Automated: every 8 hours, every 5 GB, or on a Automated
    - schedule. Set retention between 1 to 35 days
/ Manual
- Manual: snapshot is retained until you delete it Copy
    - Region
(eu-west-1)
- You can configure Amazon Redshift to
    - automatically copy snapshots (automated or
Restore Copied
manual) of a cluster to another AWS Region
Snapshot
Redshift Cluster
(New)


## Loading data into Redshift:

Large inser ts are MUCH better
Amazon Kinesis S3 using COPY command EC2 Instance
Data Firehose JDBC driver
Internet
Without Enhanced VPC Routing
Through VPC
With Enhanced VPC Routing
Amazon Kinesis Amazon Redshift
S3 Bucket Amazon Redshift EC2 Instance Amazon Redshift
Data Firehose Cluster
(mybucket) Cluster Cluster
(through S3 copy) Better to write
Data in batches
copy customer
from 's3://mybucket/mydata’
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';


## Redshift Spectrum

SELECT COUNT (*), …
Query FROM S3.EXT_TABLE
GROUP BY …
JDBC/ODBC
- Query data that is already in
    - Amazon Redshift Cluster
S3 without loading it
Leader Node
- Must have a Redshift cluster
    - available to start the query
- The query is then submitted Compute Nodes
    - to thousands of Redshift
Spectrum nodes
Redshift Spectrum
1 2 …. N
Amazon S3


## Amazon OpenSearch Service

- Amazon OpenSearch is successor to Amazon ElasticSearch
- In DynamoDB, queries only exist by primary key or indexes…
- With OpenSearch, you can search any field, even partially matches
- It’s common to use OpenSearch as a complement to another database
- Two modes: managed cluster or serverless cluster
- Does not natively support SQL (can be enabled via a plugin)
- Ingestion from Kinesis Data Firehose, AWS IoT, and CloudWatch Logs
- Security through Cognito & IAM, KMS encryption, TLS
- Comes with OpenSearch Dashboards (visualization)


## OpenSearch patterns

DynamoDB
CRUD
DynamoDB Table DynamoDB Stream Lambda Function Amazon OpenSearch
API to search items
API to retrieve items


## OpenSearch patterns

CloudWatch Logs
Real time
CloudWatch Logs Subscription Filter Lambda Function Amazon OpenSearch
(managed by AWS)
Near Real Time
CloudWatch Logs Subscription Filter Kinesis Data Firehose Amazon OpenSearch


## OpenSearch patterns

Kinesis Data Streams & Kinesis Data Firehose
Kinesis Data
Kinesis Data
Streams
Streams
Lambda
Function
Kinesis Data
data Lambda
Firehose
transformation
Function
(near real time)
(real time)
Amazon
Amazon
OpenSearch
OpenSearch


## Amazon EMR

- EMR stands for “Elastic MapReduce”
- EMR helps creating Hadoop clusters (Big Data) to analyze and process
    - vast amount of data
- The clusters can be made of hundreds of EC2 instances
- EMR comes bundled with Apache Spark, HBase, Presto, Flink…
- EMR takes care of all the provisioning and configuration
- Auto-scaling and integrated with Spot instances
- Use cases: data processing, machine learning, web indexing, big data…


## Amazon EMR – Node types & purchasing

- Master Node: Manage the cluster, coordinate, manage health – long running
- Core Node: Run tasks and store data – long running
- Task Node (optional): Just to run tasks – usually Spot
- Purchasing options:
- On-demand: reliable, predictable, won’t be terminated
- Reserved (min 1 year): cost savings (EMR will automatically use if available)
- Spot Instances: cheaper, can be terminated, less reliable
- Can have long-running cluster, or transient (temporary) cluster


## Amazon QuickSight

- Serverless machine learning-powered business intelligence service to create
    - interactive dashboards
- Fast, automatically scalable, embeddable, with per-session pricing
- Use cases:
- Business analytics
- Building visualizations
- Perform ad-hoc analysis
- Get business insights using data
- Integrated with RDS, Aurora,
    - Athena, Redshift, S3…
- In-memory computation using SPICE
    - engine if data is imported into QuickSight
- Enterprise edition: Possibility to setup
    - Column-Level security (CLS)
https://aws.amazon.com/quicksight/


## QuickSight Integrations

Data Sources (AWS Services)
On-Premises
QuickSight
Databases (JDBC)
RDS Aurora Redshift
Data Sources (Imports)
Athena S3 OpenSearch
Data Sources (SaaS)
ELF & CLF
Timestream
(Log Format)


## QuickSight – Dashboard & Analysis

- Define Users (standard versions) and Groups (enterprise version)
- These users & groups only exist within QuickSight, not IAM !!
- A dashboard…
- is a read-only snapshot of an analysis that you can share
- preserves the configuration of the analysis (filtering, parameters, controls, sort)
- You can share the analysis or the dashboard with Users or Groups
- To share a dashboard, you must first publish it
- Users who see the dashboard can also see the underlying data


## AWS Glue

- Managed extract, transform, and load (ETL) service
- Useful to prepare and transform data for analytics
- Fully serverless service
    - Glue ETL
S3 Bucket
Extract Load
Amazon RDS Redshift
Transform
Data Warehouse


## AWS Glue – Conver t data into Parquet format

Glue ETL
Import CSV
S3 Put Parquet Analyze
Amazon
Athena
Input Output
S3 Bucket S3 Bucket
Trigger
Glue ETL Job
Event notifications
On S3 PUT
Lambda Function
(EventBridge works as an alternative)


## Glue Data Catalog: catalog of datasets

Glue Jobs (ETL)
Amazon Athena
Amazon S3
Data discovery
Writes Metadata
AWS Glue
Amazon RDS
Data Catalog
Amazon
AWS Glue
Redshift
Data Crawler Database Database
Spectrum
Amazon DynamoDB
JDBC Tables Tables
(Metadata) (Metadata)
Amazon EMR


## Glue – things to know at a high-level

- Glue Job Bookmarks: prevent re-processing old data
- Glue DataBrew: clean and normalize data using pre-built transformation
- Glue Studio: new GUI to create, run and monitor ETL jobs in Glue
- Glue Streaming ETL (built on Apache Spark Structured Streaming):

compatible with Kinesis Data Streaming, Kafka, MSK (managed Kafka)


## AWS Lake Formation

- Data lake = central place to have all your data for analytics purposes
- Fully managed service that makes it easy to setup a data lake in days
- Discover, cleanse, transform, and ingest data into your Data Lake
- It automates many complex manual steps (collecting, cleansing, moving,
    - cataloging data, …) and de-duplicate (using ML Transforms)
- Combine structured and unstructured data in the data lake
- Out-of-the-box source blueprints: S3, RDS, Relational & NoSQL DB…
- Fine-grained Access Control for your applications (row and column-level)
- Built on top of AWS Glue


## AWS Lake Formation

Data Sources
Athena
Source Crawlers
Amazon S3 ETL and Data Prep.
ingest
Data Catalog Redshift
Security Settings Users
RDS Aurora
Access Control
EMR
AWS Lake Formation
On-Premises
Database (SQL & NoSQL)
Data Lake
(stored in S3)


## AWS Lake Formation

Centralized Permissions Example
Data Sources
ingest
Access Control
Amazon S3
Column-level security
Athena Users
AWS Lake Formation Quicksight
RDS Aurora
Data Lake
(stored in S3)


## Amazon Managed Service for Apache Flink

- Previously named: Kinesis Data Analytics for Apache Flink
- Flink (Java, Scala or SQL) is a framework for processing data streams
    - Kinesis Data
Streams
Amazon MSK
Amazon Managed Service
(Apache Kafka)
for Apache Flink
- Run any Apache Flink application on a managed cluster on AWS
- Provisioned compute resources, parallel computation, automatic scaling
- Application backups (implemented as checkpoints and snapshots)
- Use any Apache Flink programming features to transform data
- Important: Flink does not read from Amazon Data Firehose


## Amazon Managed Streaming for Apache

Kafka (Amazon MSK)
- Alternative to Amazon Kinesis
- Fully managed Apache Kafka on AWS
- Allow you to create, update, delete clusters
- MSK creates & manages Kafka brokers nodes & Zookeeper nodes for you
- Deploy the MSK cluster in your VPC, multi-AZ (up to 3 for HA)
- Automatic recovery from common Apache Kafka failures
- Data is stored on EBS volumes for as long as you want
- MSK Serverless
- Run Apache Kafka on MSK without managing the capacity
- MSK automatically provisions resources and scales compute & storage


## Apache Kafka at a high level

EMR
MSK Cluster
Kinesis
n S3
o
ti
a Broker 2
c
pli
e
r
IoT
SageMaker
Producers Write to topic Poll from topic Consumers
(your code) (your code)
Broker 1
r
e
p
RDS lic Kinesis
a
t
io
n
Etc…
RDS
Broker 3
Etc…


## Kinesis Data Streams vs. Amazon MSK

Kinesis Data Streams Amazon MSK
- 1 MB message size limit • 1MB default, configure for higher (ex: 10MB)
- Data Streams with Shards • Kafka Topics with Partitions
- Shard Splitting & Merging • Can only add partitions to a topic
- TLS In-flight encryption • PLAINTEXT or TLS In-flight Encryption
- KMS at-rest encryption • KMS at-rest encryption


## Amazon MSK Consumers

Kinesis Data Analytics
for Apache Flink
AWS Glue
Streaming ETL Jobs
Powered by Apache Spark Streaming
Lambda
Amazon MSK
Applications Running on
Amazon EC2 ECS EKS


## Big Data Ingestion Pipeline

- We want the ingestion pipeline to be fully serverless
- We want to collect data in real time
- We want to transform the data
- We want to query the transformed data using SQL
- The reports created using the queries should be in S3
- We want to load that data into a warehouse and create dashboards


## Big Data Ingestion Pipeline

Pull data
IoT Devices
Real-time Reporting
Ingestion
Every 1 minute Bucket
Bucket
trigger
Amazon Kinesis Data Amazon Kinesis Data Amazon Simple Storage Amazon Simple Queue AWS Lambda Amazon Athena Amazon Simple Storage
Streams Firehose Service (S3) Service Service (S3)
(optional)
AWS Lambda
Amazon QuickSight
Amazon Redshift
Serverless


## Big Data Ingestion Pipeline discussion

- IoT Core allows you to harvest data from IoT devices
- Kinesis is great for real-time data collection
- Firehose helps with data delivery to S3 in near real-time (1 minute)
- Lambda can help Firehose with data transformations
- Amazon S3 can trigger notifications to SQS
- Lambda can subscribe to SQS (we could have connecter S3 to Lambda)
- Athena is a serverless SQL service and results are stored in S3
- The reporting bucket contains analyzed data and can be used by
    - reporting tool such as AWS QuickSight, Redshift, etc…
