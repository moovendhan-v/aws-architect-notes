# Serverless Architectures


## Mobile application: MyTodoList

- We want to create a mobile application with the following requirements
- Expose as REST API with HTTPS
- Serverless architecture
- Users should be able to directly interact with their own folder in S3
- Users should authenticate through a managed serverless service
- The users can write and read to-dos, but they mostly read them
- The database should scale, and have some high read throughput


## Mobile app: REST API layer

REST HTTPS invoke query
AWS Lambda Amazon DynamoDB
Amazon API Gateway
Mobile
client
Verify authentication
authenticate
Amazon Cognito


## Mobile app: giving users access to S3

Store/retrieve files
Amazon S3
Permissions
AWS Lambda Amazon DynamoDB
Amazon API Gateway
Mobile
client
authenticate
Amazon Cognito


## Mobile app: high read throughput, static data

Store/retrieve files
Amazon S3
Permissions
REST HTTPS invoke Query / read
AWS Lambda
Amazon API Gateway DAX DynamoDB
Mobile
Caching layer
client
Verify authentication
authenticate
Amazon Cognito


## Mobile app: caching at the API Gateway

Store/retrieve files
Amazon S3
CACHING OF RESPONSES
Permissions
REST HTTPS invoke Query / read
AWS Lambda
Amazon API Gateway DAX DynamoDB
Mobile
Caching layer
client
Verify authentication
authenticate
Amazon Cognito


## In this lecture

- Serverless REST API: HTTPS, API Gateway, Lambda, DynamoDB
- Using Cognito to generate temporary credentials to access S3 bucket

with restricted policy. App users can directly access AWS resources this
way. Pattern can be applied to DynamoDB, Lambda…
- Caching the reads on DynamoDB using DAX
- Caching the REST requests at the API Gateway level
- Security for authentication and authorization with Cognito


## Serverless hosted website: MyBlog.com

- This website should scale globally
- Blogs are rarely written, but often read
- Some of the website is purely static files, the rest is a dynamic REST API
- Caching must be implement where possible
- Any new users that subscribes should receive a welcome email
- Any photo uploaded to the blog should have a thumbnail generated


## Ser ving static content, globally

Interaction with
edge locations
Amazon S3
Amazon CloudFront
Global distribution
Client


## Ser ving static content, globally, securely

OAC: Origin Access Control
Bucket policy
Only authorize from
Interaction with
CloudFront Distribution
edge locations
Amazon S3
Amazon CloudFront
Global distribution
Client


## Adding a public serverless REST API

OAC: Origin Access Control
Bucket policy
Only authorize from
Interaction with
CloudFront Distribution
edge locations
Amazon S3
Amazon CloudFront
Global distribution
REST HTTPS invoke Query / read
AWS Lambda
Amazon API Gateway DAX DynamoDB
Client
Caching layer


## Leveraging DynamoDB Global Tables

OAC: Origin Access Control
Bucket policy
Only authorize from
Interaction with
CloudFront Distribution
edge locations
Amazon S3
Amazon CloudFront
Global distribution
REST HTTPS invoke Query / read
AWS Lambda
Amazon API Gateway DAX DynamoDB
Client
Caching layer Global Tables


## User Welcome email flow

OAC: Origin Access Control
Bucket policy
Only authorize from
Interaction with
CloudFront Distribution
edge locations
Amazon S3
Amazon CloudFront
Global distribution
REST HTTPS invoke Query / read
AWS Lambda
Amazon API Gateway DAX DynamoDB
Client
Caching layer
Stream changes
IAM Role
SDK to send email
Invoke lambda
Amazon Simple AWS Lambda
DynamoDB
Email Service (SES)
Stream


## Thumbnail Generation flow

OAC: Origin Access Control
Bucket policy
Only authorize from
Interaction with
CloudFront Distribution
edge locations
Amazon S3
Amazon CloudFront
Global distribution
REST HTTPS invoke Query / read
AWS Lambda
Amazon API Gateway DAX DynamoDB
Client
Caching layer
optional
Upload photos
Transfer acceleration OAC trigger thumbnail
SQS
Amazon CloudFront Amazon S3 AWS Lambda Amazon S3
Global distribution
SNS


## AWS Hosted Website Summar y

- We’ve seen static content being distributed using CloudFront with S3
- The REST API was serverless, didn’t need Cognito because public
- We leveraged a Global DynamoDB table to serve the data globally
- (we could have used Aurora Global Database)
- We enabled DynamoDB streams to trigger a Lambda function
- The lambda function had an IAM role which could use SES
- SES (Simple Email Service) was used to send emails in a serverless way
- S3 can trigger SQS / SNS / Lambda to notify of events


## Micro Services architecture

- We want to switch to a micro service architecture
- Many services interact with each other directly using a REST API
- Each architecture for each micro service may vary in form and shape
- We want a micro-service architecture so we can have a leaner
    - development lifecycle for each service


## Micro Services Environment

service1.example.com
DNS Query
Amazon Route 53
Elastic Load Balancing ECS DynamoDB
service2.example.com
HTTPS
Amazon API Gateway AWS Lambda ElastiCache
Users
service3.example.com
Elastic Load Balancing Amazon EC2
Amazon RDS
Auto Scaling


## Discussions on Micro Services

- You are free to design each micro-service the way you want
- Synchronous patterns: API Gateway, Load Balancers
- Asynchronous patterns: SQS, Kinesis, SNS, Lambda triggers (S3)
- Challenges with micro-services:
- repeated overhead for creating each new microservice,
- issues with optimizing server density/utilization
- complexity of running multiple versions of multiple microservices simultaneously
- proliferation of client-side code requirements to integrate with many separate services.
- Some of the challenges are solved by Serverless patterns:
- API Gateway, Lambda scale automatically and you pay per usage
- You can easily clone API, reproduce environments
- Generated client SDK through Swagger integration for the API Gateway


## Software updates offloading

- We have an application running on EC2, that distributes software
    - updates once in a while
- When a new software update is out, we get a lot of request and the

content is distributed in mass over the network. It’s very costly
- We don’t want to change our application, but want to optimize our cost
    - and CPU, how can we do it?


## Our application current state

Auto Scaling group
Availability zone 1
Availability zone 1 to 3
Availability zone 2
Amazon Elastic
File System
Availability zone 3


## Easy way to fix things!

Auto Scaling group
Availability zone 1
Availability zone 1 to 3
Availability zone 2
Amazon Elastic
Amazon CloudFront File System
Availability zone 3


## Why CloudFront?

- No changes to architecture
- Will cache software update files at the edge
- Software update files are not dynamic, they’re static (never changing)
- Our EC2 instances aren’t serverless
- But CloudFront is, and will scale for us
- Our ASG will not scale as much, and we’ll save tremendously in EC2
- We’ll also save in availability, network bandwidth cost, etc
- Easy way to make an existing application more scalable and cheaper!
