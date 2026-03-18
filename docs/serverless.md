# Serverless Overview


## What’s serverless?

- Serverless is a new paradigm in which the developers don’t have to
    - manage servers anymore…
- They just deploy code
- They just deploy… functions !
- Initially... Serverless == FaaS (Function as a Service)
- Serverless was pioneered by AWS Lambda but now also includes

anything that’s managed: “databases, messaging, storage, etc.”
- Serverless does not mean there are no servers…
    - it means you just don’t manage / provision / see them


## Serverless in AWS

t
n
e • AWS Lambda t
n
o
c
- DynamoDB c
    - i
t
a
t
S
- AWS Cognito
- AWS API Gateway
- Amazon S3
- AWS SNS & SQS
- AWS Kinesis Data Firehose
- Aurora Serverless
- Step Functions
- Fargate
    - IPA
TSER
Users
L
o
g
i
n
S3 bucket API Gateway
Cognito
Lambda
DynamoDB


## Why AWS Lambda

- Virtual Servers in the Cloud
- Limited by RAM and CPU
- Continuously running
- Scaling means intervention to add / remove servers
    - Amazon EC2
- Virtual functions – no servers to manage!
- Limited by time - short executions
- Run on-demand
    - Amazon Lambda • Scaling is automated!


## Benefits of AWS Lambda

- Easy Pricing:
- Pay per request and compute time
- Free tier of 1,000,000 AWS Lambda requests and 400,000 GBs of compute time
- Integrated with the whole AWS suite of services
- Integrated with many programming languages
- Easy monitoring through AWS CloudWatch
- Easy to get more resources per functions (up to 10GB of RAM!)
- Increasing RAM will also improve CPU and network!


## AWS Lambda language suppor t

- Node.js (JavaScript)
- Python
- Java
- C# (.NET Core) / Powershell
- Ruby
- Custom Runtime API (community supported, example Rust or Golang)
- Lambda Container Image
- The container image must implement the Lambda Runtime API
- ECS / Fargate is preferred for running arbitrary Docker images


## AWS Lambda Integrations

Main ones
API Gateway Kinesis DynamoDB S3 CloudFront
CloudWatch Events CloudWatch Logs SNS SQS Cognito
EventBridge


## Example: Serverless Thumbnail creation

h
s
u
p
New thumbnail in S3
trigger
p
u
s
h Image name
AWS Lambda Function Image size
New image in S3
Creates a Thumbnail
Creation date
etc…
Metadata in DynamoDB


## Example: Serverless CRON Job

Trigger
Every 1 hour
CloudWatch Events
AWS Lambda Function
EventBridge
Perform a task


## AWS Lambda Pricing: example

- You can find overall pricing information here:
    - https://aws.amazon.com/lambda/pricing/
- Pay per calls:
- First 1,000,000 requests are free
- $0.20 per 1 million requests thereafter ($0.0000002 per request)
- Pay per duration: (in increment of 1 ms)
- 400,000 GB-seconds of compute time per month for FREE
- == 400,000 seconds if function is 1GB RAM
- == 3,200,000 seconds if function is 128 MB RAM
- After that $1.00 for 600,000 GB-seconds
- It is usually very cheap to run AWS Lambda so it’s very popular


## AWS Lambda Limits to Know - per region

- Execution:
- Memory allocation: 128 MB – 10GB (1 MB increments)
- Maximum execution time: 900 seconds (15 minutes)
- Environment variables (4 KB)
- Disk capacity in the “function container” (in /tmp): 512 MB to 10GB
- Concurrency executions: 1000 (can be increased)
- Deployment:
- Lambda function deployment size (compressed .zip): 50 MB
- Size of uncompressed deployment (code + dependencies): 250 MB
- Can use the /tmp directory to load other files at startup
- Size of environment variables: 4 KB


## Lambda Concurrency and Throttling

- Concurrency limit: up to 1000 concurrent executions
- Can set a “reserved concurrency” at the function level (=limit)
- Each invocation over the concurrency limit will trigger a “Throttle”
- Throttle behavior:
- If synchronous invocation => return ThrottleError - 429
- If asynchronous invocation => retry automatically and then go to DLQ
- If you need a higher limit, open a support ticket


## Lambda Concurrency Issue

- If you don’t reserve (=limit) concurrency, the following can happen:
    - 1000 concurrent
executions
Many users
Application Load Balancer
THROTTLE!
Few users
API Gateway
THROTTLE!
SDK / CLI


## Concurrency and Asynchronous Invocations

- If the function doesn't have enough
    - concurrency available to process all
events, additional requests are
throttled.
New file event
- For throttling errors (429) and
    - system errors (500-series), Lambda
New file event
returns the event to the queue and
attempts to run the function again
S3 bucket
for up to 6 hours.
- The retry interval increases
    - New file event
exponentially from 1 second after
the first attempt to a maximum of
5 minutes.


## Cold Star ts & Provisioned Concurrency

- Cold Start:
- New instance => code is loaded and code outside the handler run (init)
- If the init is large (code, dependencies, SDK…) this process can take some time.
- First request served by new instances has higher latency than the rest
- Provisioned Concurrency:
- Concurrency is allocated before the function is invoked (in advance)
- So the cold start never happens and all invocations have low latency
- Application Auto Scaling can manage concurrency (schedule or target utilization)
- Note:
- Note: cold starts in VPC have been dramatically reduced in Oct & Nov 2019
- https://aws.amazon.com/blogs/compute/announcing-improved-vpc-networking-for-aws-lambda-functions/


## Reser ved and Provisioned Concurrency

https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html


## Lambda SnapStar t

SnapStart SnapStart
- Improves your Lambda functions performance enabled disabled
    - up to 10x at no extra cost for Java, Python & .NET
invoke invoke
- When enabled, function is invoked from a pre-
    - initialized state (no function initialization from
Lambda Lambda
scratch)
function is
- When you publish a new version: pre-initialized
    - Init
- Lambda initializes your function
- Takes a snapshot of memory and disk state of the Invoke Invoke
    - initialized function
Shutdown Shutdown
- Snapshot is cached for low-latency access
    - Lambda Invocation
Lifecycle Phases


## Customization At The Edge

- Many modern applications execute some form of the logic at the edge
- Edge Function:
- A code that you write and attach to CloudFront distributions
- Runs close to your users to minimize latency
- CloudFront provides two types: CloudFront Functions & Lambda@Edge
- You don’t have to manage any servers, deployed globally
- Use case: customize the CDN content
- Pay only for what you use
- Fully serverless


## CloudFront Functions & Lambda@Edge

Use Cases
- Website Security and Privacy
- Dynamic Web Application at the Edge
- Search Engine Optimization (SEO)
- Intelligently Route Across Origins and Data Centers
- Bot Mitigation at the Edge
- Real-time Image Transformation
- A/B Testing
- User Authentication and Authorization
- User Prioritization
- User Tracking and Analytics


## CloudFront Functions

Client
- Lightweight functions written in JavaScript
- For high-scale, latency-sensitive CDN customizations
    - Viewer Viewer
Request Response
- Sub-ms startup times, millions of requests/second
- Used to change Viewer requests and responses:
- Viewer Request: after CloudFront receives a request from a
    - CloudFront
viewer
- Viewer Response: before CloudFront forwards the response
    - Origin Origin
to the viewer
Request Response
- Native feature of CloudFront (manage code entirely
    - within CloudFront)
Origin


## Lambda@Edge

Client
- Lambda functions written in NodeJS or Python
- Scales to 1000s of requests/second
    - Viewer Viewer
Request Response
- Used to change CloudFront requests and responses:
- Viewer Request – after CloudFront receives a request from a
    - viewer
- Origin Request – before CloudFront forwards the request to the
    - origin
CloudFront
- Origin Response – after CloudFront receives the response from the
    - origin
Origin Origin
- Viewer Response – before CloudFront forwards the response to
    - Request Response
the viewer
- Author your functions in one AWS Region (us-east-1), then
    - CloudFront replicates to its locations
Origin


## CloudFront Functions vs. Lambda@Edge

CloudFront Functions Lambda@Edge
Runtime Support JavaScript Node.js, Python
# of Requests Millions of requests per second Thousands of requests per second
CloudFront Triggers - Viewer Request/Response - Viewer Request/Response
- Origin Request/Response
    - Max. Execution Time < 1 ms 5 – 10 seconds
Max. Memory 2 MB 128 MB up to 10 GB
Total Package Size 10 KB 1 MB – 50 MB
Network Access, File System Access No Yes
Access to the Request Body No Yes
Pricing Free tier available, 1/6th price of @Edge No free tier, charged per request & duration


## CloudFront Functions vs. Lambda@Edge - Use Cases

CloudFront Functions
Lambda@Edge
- Cache key normalization
- Longer execution time (several ms)
- Transform request attributes (headers,
- Adjustable CPU or memory
    - cookies, query strings, URL) to create an
optimal Cache Key
- Your code depends on a 3rd
- Header manipulation
    - libraries (e.g., AWS SDK to access
- Insert/modify/delete HTTP headers in the
    - other AWS services)
request or response
- URL rewrites or redirects • Network access to use external
- Request authentication & authorization services for processing
- Create and validate user-generated
- File system access or access to the
    - tokens (e.g., JWT) to allow/deny requests
body of HTTP requests


## Lambda by default

Default Lambda Deployment
- By default, your Lambda function is
    - launched outside your own VPC (in AWS Cloud
an AWS-owned VPC)
Public
www
- Therefore, it cannot access resources
    - works
DynamoDB
in your VPC (RDS, ElastiCache,
internal ELB…)
VPC & Private Subnet
Not working
Private RDS


## Lambda in VPC

Lambda Function
- You must define the VPC ID, the
    - Private subnet
Subnets and the Security Groups
- Lambda will create an ENI (Elastic
    - Lambda Security group
Network Interface) in your subnets
Elastic Network
Interface (ENI)
RDS Security group
Amazon RDS
In VPC


## Lambda with RDS Proxy

VPC
Lambda functions
- If Lambda functions directly access your
    - …
database, they may open too many
connections under high load
- RDS Proxy
- Improve scalability by pooling and sharing DB
    - connections
Private subnet
- Improve availability by reducing by 66% the
    - failover time and preserving connections RDS Proxy
- Improve security by enforcing IAM
    - authentication and storing credentials in
Secrets Manager
- The Lambda function must be deployed in
    - RDS DB
your VPC, because RDS Proxy is never
Instance
publicly accessible


## Invoking Lambda from RDS & Aurora

User
- Invoke Lambda functions from within your DB instance
    - register
- Allows you to process data events from within a database (INSERT)
    - RDS DB
- Supported for RDS for PostgreSQL and Aurora MySQL
    - Instance
- Must allow outbound traffic to your Lambda function
    - Permissions
invoke
from within your DB instance (Public, NAT GW, VPC
Endpoints)
Lambda
- DB instance must have the required permissions to
    - function
invoke the Lambda function (Lambda Resource-based
send Email
Policy & IAM Policy)
Amazon SES


## RDS Event Notifications

RDS DB
Instance
- Notifications that tells information about the DB
    - instance itself (created, stopped, start, …)
- You don’t have any information about the data itself
- Subscribe to the following event categories: DB
    - instance, DB snapshot, DB Parameter Group, DB
Security Group, RDS Proxy, Custom Engine Version
SNS EventBridge
- Near real-time events (up to 5 minutes)
- Send notifications to SNS or subscribe to events
    - using EventBridge
…
SQS
Lambda Lambda
Queue
function function


## Amazon DynamoDB

- Fully managed, highly available with replication across multiple AZs
- NoSQL database - not a relational database - with transaction support
- Scales to massive workloads, distributed database
- Millions of requests per seconds, trillions of row, 100s of TB of storage
- Fast and consistent in performance (single-digit millisecond)
- Integrated with IAM for security, authorization and administration
- Low cost and auto-scaling capabilities
- No maintenance or patching, always available
- Standard & Infrequent Access (IA) Table Class


## DynamoDB - Basics

- DynamoDB is made of Tables
- Each table has a Primary Key (must be decided at creation time)
- Each table can have an infinite number of items (= rows)
- Each item has attributes (can be added over time – can be null)
- Maximum size of an item is 400KB
- Data types supported are:
- Scalar Types – String, Number, Binary, Boolean, Null
- Document Types – List, Map
- Set Types – String Set, Number Set, Binary Set
- Therefore, in DynamoDB you can rapidly evolve schemas


## DynamoDB – Table example

Primary Key Attributes
Partition Key Sort Key
User_ID Game_ID Score Result
7791a3d6-… 4421 92 Win
873e0634-… 1894 14 Lose
873e0634-… 4521 77 Win


## DynamoDB – Read/Write Capacity Modes

- Control how you manage your table’s capacity (read/write throughput)
- Provisioned Mode (default)
- You specify the number of reads/writes per second
- You need to plan capacity beforehand
- Pay for provisioned Read Capacity Units (RCU) & Write Capacity Units (WCU)
- Possibility to add auto-scaling mode for RCU & WCU
- On-Demand Mode
- Read/writes automatically scale up/down with your workloads
- No capacity planning needed
- Pay for what you use, more expensive ($$$)
- Great for unpredictable workloads, steep sudden spikes


## DynamoDB Accelerator (DAX)

Application
- Fully-managed, highly available, seamless in-
    - memory cache for DynamoDB
- Help solve read congestion by caching
    - DAX Cluster
- Microseconds latency for cached data
    - …
- Doesn’t require application logic modification
    - Nodes
(compatible with existing DynamoDB APIs)
- 5 minutes TTL for cache (default)
    - Amazon DynamoDB
…
Tables


## DynamoDB Accelerator (DAX) vs. ElastiCache

Amazon
ElastiCache
Store Aggregation Result
- Individual objects cache
    - Application
- Query & Scan cache
    - Amazon
DynamoDB
DynamoDB Accelerator (DAX)


## DynamoDB – Stream Processing

- Ordered stream of item-level modifications (create/update/delete) in a table
- Use cases:
- React to changes in real-time (welcome email to users)
- Real-time usage analytics
- Insert into derivative tables
- Implement cross-region replication
- Invoke AWS Lambda on changes to your DynamoDB table
    - DynamoDB Streams Kinesis Data Streams (newer)
- 24 hours retention • 1 year retention
- Limited # of consumers • High # of consumers
- Process using AWS Lambda Triggers, or • Process using AWS Lambda, Kinesis Data
    - Analytics, Kineis Data Firehose, AWS Glue
DynamoDB Stream Kinesis adapter
Streaming ETL…


## DynamoDB Streams

messaging, notifications
Processing Layer Amazon SNS
DynamoDB
KCL Adapter
filtering, transforming, …
DDB Table
Lambda
create/update/delete
Application DynamoDB
Table analytics Amazon
Streams
Redshift
archiving
Amazon S3
Kinesis Data Kinesis Data
Streams Firehose
indexing Amazon
OpenSearch


## DynamoDB Global Tables

GLOBAL TABLE
Table Table
US-EAST-1 AP-SOUTHEAST-2
two-way
replication
- Make a DynamoDB table accessible with low latency in multiple-regions
- Active-Active replication
- Applications can READ and WRITE to the table in any region
- Must enable DynamoDB Streams as a pre-requisite


## DynamoDB – Time To Live (TTL)

Current Time
Friday, September 10, 2021, 11:56:11 AM
(Epoch timestamp: 1631274971)
Expiration Process
scan &
expire items
- Automatically delete items after an expiry
    - timestamp SessionData (Table)
- Use cases: reduce stored data by keeping only User_ID Session_ID ExpTime (TTL)

current items, adhere to regulatory 7791a3d6-… 74686572652 1631188571
obligations, web session handling… 873e0634-… 6e6f7468696 1631274971
a80f73a1-… 746f2073656 1631102171
scan &
delete items
Deletion Process


## DynamoDB – Backups for disaster recovery

- Continuous backups using point-in-time recovery (PITR)
- Optionally enabled for the last 35 days
- Point-in-time recovery to any time within the backup window
- The recovery process creates a new table
- On-demand backups
- Full backups for long-term retention, until explicitely deleted
- Doesn’t affect performance or latency
- Can be configured and managed in AWS Backup (enables cross-region copy)
- The recovery process creates a new table


## DynamoDB – Integration with Amazon S3

- Export to S3 (must enable PITR)
- Works for any point of time in the last 35 days
- Doesn’t affect the read capacity of your table
- Perform data analysis on top of DynamoDB export query
- Retain snapshots for auditing
- ETL on top of S3 data before importing back into
    - DynamoDB S3 Athena
DynamoDB
- Export in DynamoDB JSON or ION format
- Import from S3
- Import CSV, DynamoDB JSON or ION format
    - import
- Doesn’t consume any write capacity
- Creates a new table
    - S3 DynamoDB
- Import errors are logged in CloudWatch Logs
    - (.csv, .json, .ion)


## Example: Building a Serverless API

REST API PROXY REQUESTS CRUD
Client API Gateway Lambda DynamoDB


## AWS API Gateway

- AWS Lambda + API Gateway: No infrastructure to manage
- Support for the WebSocket Protocol
- Handle API versioning (v1, v2…)
- Handle different environments (dev, test, prod…)
- Handle security (Authentication and Authorization)
- Create API keys, handle request throttling
- Swagger / Open API import to quickly define APIs
- Transform and validate requests and responses
- Generate SDK and API specifications
- Cache API responses


## API Gateway – Integrations High Level

- Lambda Function
- Invoke Lambda function
- Easy way to expose REST API backed by AWS Lambda
- HTTP
- Expose HTTP endpoints in the backend
- Example: internal HTTP API on premise, Application Load Balancer…
- Why? Add rate limiting, caching, user authentications, API keys, etc…
- AWS Service
- Expose any AWS API through the API Gateway
- Example: start an AWS Step Function workflow, post a message to SQS
- Why? Add authentication, deploy publicly, rate control…


## API Gateway – AWS Service Integration

Kinesis Data Streams example
store .json
requests send records files
API Gateway Kinesis Data Kinesis Data Amazon S3
Client
Streams Firehose


## API Gateway - Endpoint Types

- Edge-Optimized (default): For global clients
- Requests are routed through the CloudFront Edge locations (improves latency)
- The API Gateway still lives in only one region
- Regional:
- For clients within the same region
- Could manually combine with CloudFront (more control over the caching
    - strategies and the distribution)
- Private:
- Can only be accessed from your VPC using an interface VPC endpoint (ENI)
- Use a resource policy to define access


## API Gateway – Security

- User Authentication through
- IAM Roles (useful for internal applications)
- Cognito (identity for external users – example mobile users)
- Custom Authorizer (your own logic)
- Custom Domain Name HTTPS security through integration with AWS
    - Certificate Manager (ACM)
- If using Edge-Optimized endpoint, then the certificate must be in us-east-1
- If using Regional endpoint, the certificate must be in the API Gateway region
- Must setup CNAME or A-alias record in Route 53


## AWS Step Functions

- Build serverless visual workflow to
    - orchestrate your Lambda functions
- Features: sequence, parallel, conditions,
    - timeouts, error handling, …
- Can integrate with EC2, ECS, On-premises
    - servers, API Gateway, SQS queues, etc…
- Possibility of implementing human approval
    - feature
- Use cases: order fulfillment, data processing,
    - web applications, any workflow


## Amazon Cognito

- Give users an identity to interact with our web or mobile application
- Cognito User Pools:
- Sign in functionality for app users
- Integrate with API Gateway & Application Load Balancer
- Cognito Identity Pools (Federated Identity):
- Provide AWS credentials to users so they can access AWS resources directly
- Integrate with Cognito User Pools as an identity provider
- Cognito vs IAM: “hundreds of users”, ”mobile users”, “authenticate with SAML”


## Cognito User Pools (CUP) – User Features

- Create a serverless database of user for your web & mobile apps
- Simple login: Username (or email) / password combination
- Password reset
- Email & Phone Number Verification
- Multi-factor authentication (MFA)
- Federated Identities: users from Facebook, Google, SAML…


## Cognito User Pools (CUP) - Integrations

- CUP integrates with API Gateway and Application Load Balancer
    - Cognito User Pools
Authenticate Authenticate
Cognito User Pools
Retrieve token
Evaluate Cognito Token
REST API +
Pass Token Application Load Balancer
+ Listeners & Rules
backend Target Group
API Gateway
Backend


## Cognito Identity Pools (Federated Identities)

- Get identities for “users” so they obtain temporary AWS credentials
- Users source can be Cognito User Pools, 3rd party logins, etc…
- Users can then access AWS services directly or through API Gateway
- The IAM policies applied to the credentials are defined in Cognito
- They can be customized based on the user_id for fine grained control
- Default IAM roles for authenticated and guest users


## Cognito Identity Pools – Diagram

Login and Get Token
Social Identity Provider
Web & Mobile Exchange token Cognito Identity Pools
for temporary
Applications
AWS credentials validate
Direct access to AWS
Cognito
User Pools
Private S3 Bucket DynamoDB Table


## Cognito Identity Pools

Row Level Security in DynamoDB
