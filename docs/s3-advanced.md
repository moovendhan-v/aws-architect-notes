# Amazon S3 – Advanced


## Amazon S3 – Moving between Storage Classes

- You can transition objects between
    - storage classes
Standard
Standard IA
- For infrequently accessed object,
    - move them to Standard IA
Intelligent Tiering
- For archive objects that you don’t
    - One-Zone IA
need fast access to, move them to
Glacier or Glacier Deep Archive
Glacier Instant Retrieval
Glacier Flexible Retrieval
- Moving objects can be automated
    - using a Lifecycle Rules
Glacier Deep Archive


## Amazon S3 – Lifecycle Rules

- Transition Actions – configure objects to transition to another storage class
- Move objects to Standard IA class 60 days after creation
- Move to Glacier for archiving after 6 months
- Expiration actions – configure objects to expire (delete) after some time
- Access log files can be set to delete after a 365 days
- Can be used to delete old versions of files (if versioning is enabled)
- Can be used to delete incomplete Multi-Part uploads
- Rules can be created for a certain prefix (example: s3://mybucket/mp3/*)
- Rules can be created for certain objects Tags (example: Department: Finance)


## Amazon S3 – Lifecycle Rules (Scenario 1)

- Your application on EC2 creates images thumbnails after profile photos

are uploaded to Amazon S3. These thumbnails can be easily recreated,
and only need to be kept for 60 days. The source images should be able
to be immediately retrieved for these 60 days, and afterwards, the user
can wait up to 6 hours. How would you design this?
- S3 source images can be on Standard, with a lifecycle configuration to
    - transition them to Glacier after 60 days
- S3 thumbnails can be on One-Zone IA, with a lifecycle configuration to
    - expire them (delete them) after 60 days


## Amazon S3 – Lifecycle Rules (Scenario 2)

- A rule in your company states that you should be able to recoveryour

deleted S3 objects immediately for 30 days, although this may happen
rarely. After this time, and for up to 365 days, deleted objects should be
recoverable within 48 hours.
- Enable S3 Versioning in order to have object versions, so that “deleted

objects” are in fact hidden by a “delete marker” and can be recovered
- Transition the “noncurrent versions” of the object to Standard IA
- Transition afterwards the “noncurrent versions” to Glacier Deep Archive


## Amazon S3 Analytics – Storage Class Analysis

- Help you decide when to transition objects to
    - S3 Bucket
the right storage class
- Recommendations for Standard and Standard IA
- Does NOT work for One-Zone IA or Glacier
- Report is updated daily
    - S3 Analytics
- 24 to 48 hours to start seeing data analysis
    - .csv report
- Good first step to put together Lifecycle Rules
    - Date StorageClass ObjectAge
(or improve them)!
8/22/2022 STANDARD 000-014
8/25/2022 STANDARD 030-044
9/6/2022 STANDARD 120-149


## S3 – Requester Pays

Standard Bucket
- In general, bucket owners pay for all
    - Owner Owner Requester
Amazon S3 storage and data transfer $$ Storage Cost $$ Networking Cost
costs associated with their bucket
download
- With Requester Pays buckets, the
    - requester instead of the bucket owner
pays the cost of the request and the
Requester Pays Bucket
data download from the bucket
Owner Requester
- Helpful when you want to share large
    - $$ Storage Cost $$ Networking Cost
datasets with other accounts
download
- The requester must be authenticated
    - in AWS (cannot be anonymous)


## S3 Event Notifications

- S3:ObjectCreated, S3:ObjectRemoved,
    - S3:ObjectRestore, S3:Replication…
- Object name filtering possible (*.jpg) SNS
- Use case: generate thumbnails of images
    - uploaded to S3
events
- Can create as many “S3 events” as desired
    - SQS
Amazon S3
- S3 event notifications typically deliver events
    - in seconds but can sometimes take a minute
or longer
Lambda Function


## S3 Event Notifications – IAM Permissions

SNS
SNS Resource (Access) Policy
events
SQS
Amazon S3
SQS Resource (Access) Policy
Lambda Function
Lambda Resource Policy


## S3 Event Notifications

with Amazon EventBridge
events All events rules
Over 18
AWS services
as destinations
Amazon S3 Amazon
bucket EventBridge
- Advanced filtering options with JSON rules (metadata, object size, name...)
- Multiple Destinations – ex Step Functions, Kinesis Streams / Firehose…
- EventBridge Capabilities – Archive, Replay Events, Reliable delivery


## S3 – Baseline Performance

- Amazon S3 automatically scales to high request rates, latency 100-200 ms
- Your application can achieve at least 3,500 PUT/COPY/POST/DELETE or

5,500 GET/HEAD requests per second per prefix in a bucket.
- There are no limits to the number of prefixes in a bucket.
- Example (object path => prefix):
- bucket/folder1/sub1/file => /folder1/sub1/
- bucket/folder1/sub2/file => /folder1/sub2/
- bucket/1/file => /1/
- bucket/2/file => /2/
- If you spread reads across all four prefixes evenly, you can achieve 22,000
    - requests per second for GET and HEAD


## S3 Performance

- Multi-Part upload: • S3 Transfer Acceleration
- recommended for files > 100MB, • Increase transfer speed by transferring

must use for files > 5GB file to an AWS edge location which will
forward the data to the S3 bucket in the
- Can help parallelize uploads (speed
    - target region
up transfers)
- Compatible with multi-part upload
    - Divide
Parallel uploads
In parts
Fast Fast
(public www) (private AWS)
File in USA Edge Location S3 Bucket
Amazon S3 USA
Australia
BIG file


## S3 Performance – S3 Byte-Range Fetches

- Parallelize GETs by requesting specific
    - byte ranges
- Better resilience in case of failures
    - Can be used to retrieve only partial
Can be used to speed up downloads data (for example the head of a file)
File in S3 File in S3
Byte-range request for header
(first XX bytes)
Part 1 Part 2 … Part N header
Requests in parallel


## S3 Batch Operations

S3 Inventory
- Perform bulk operations on existing S3 objects with a
    - single request, example:
- Modify object metadata & properties
    - Objects List Report
- Copy objects between S3 buckets
- Encrypt un-encrypted objects
- Modify ACLs, tags
    - Athena
- Restore objects from S3 Glacier
    - filter
- Invoke Lambda function to perform custom action on
    - each object
filtered list
- A job consists of a list of objects, the action to
    - operation
perform, and optional parameters
+
parameters S3 Batch
- S3 Batch Operations manages retries, tracks progress,

sends completion notifications, generate reports … Operations
User
- You can use S3 Inventory to get object list and use
    - Athena to query and filter your objects
Processed Objects
…


## S3 – Storage Lens

- Understand, analyze, and optimize storage across entire AWS Organization
- Discover anomalies, identify cost efficiencies, and apply data protection best

practices across entire AWS Organization (30 days usage & activity metrics)
- Aggregate data for Organization, specific accounts, regions, buckets, or prefixes
- Default dashboard or create your own dashboards
- Can be configured to export metrics daily to an S3 bucket (CSV, Parquet)
    - Organization
Summary Insights
Accounts
Data Protection
S3 Storage Lens Regions
Cost Efficiency
Buckets
Configure Aggregate Analyze Optimize
(Dashboard)


## Storage Lens – Default Dashboard

- Visualize summarized insights and trends for both free and advanced metrics
- Default dashboard shows Multi-Region and Multi-Account data
- Preconfigured by Amazon S3
- Can’t be deleted, but can be disabled
    - https://aws.amazon.com/blogs/aws/s3-storage-lens/
https://aws.amazon.com/blogs/aws/s3-storage-lens/


## Storage Lens – Metrics

- Summary Metrics
- General insights about your S3 storage
- StorageBytes, ObjectCount…
- Use cases: identify the fastest-growing (or not used) buckets and prefixes
- Cost-Optimization Metrics
- Provide insights to manage and optimize your storage costs
- NonCurrentVersionStorageBytes, IncompleteMultipartUploadStorageBytes…
- Use cases: identify buckets with incomplete multipart uploaded older than 7

days, Identify which objects could be transitioned to lower-cost storage class


## Storage Lens – Metrics

- Data-Protection Metrics
- Provide insights for data protection features
- VersioningEnabledBucketCount, MFADeleteEnabledBucketCount, SSEKMSEnabledBucketCount,
    - CrossRegionReplicationRuleCount…
- Use cases: identify buckets that aren’t following data-protection best practices
- Access-management Metrics
- Provide insights for S3 Object Ownership
- ObjectOwnershipBucketOwnerEnforcedBucketCount…
- Use cases: identify which Object Ownership settings your buckets use
- Event Metrics
- Provide insights for S3 Event Notifications
- EventNotificationEnabledBucketCount (identify which buckets have S3 Event Notifications
    - configured)


## Storage Lens – Metrics

- Performance Metrics
- Provide insights for S3 Transfer Acceleration
- TransferAccelerationEnabledBucketCount (identify which buckets have S3 Transfer
    - Acceleration enabled)
- Activity Metrics
- Provide insights about how your storage is requested
- AllRequests, GetRequests, PutRequests, ListRequests, BytesDownloaded…
- Detailed Status Code Metrics
- Provide insights for HTTP status codes
- 200OKStatusCount, 403ForbiddenErrorCount, 404NotFoundErrorCount…


## Storage Lens – Free vs. Paid

- Free Metrics
- Automatically available for all customers
- Contains around 28 usage metrics
- Data is available for queries for 14 days
- Advanced Metrics and Recommendations
- Additional paid metrics and features
- Advanced Metrics – Activity, Advanced Cost
    - Optimization, Advanced Data Protection, Status
Code
- CloudWatch Publishing – Access metrics in
    - CloudWatch without additional charges
- Prefix Aggregation – Collect metrics at the prefix
    - level
- Data is available for queries for 15 months
