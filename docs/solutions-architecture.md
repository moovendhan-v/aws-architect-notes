# More Solutions Architecture


## Lambda, SNS & SQS

Try, retry
retries
(poll)
SQS asynchronous
DLQ SQS + Lambda
SNS
DLQ
Try, retry
blocking
SQS
SQS FIFO
SNS + Lambda
DLQ
SQS FIFO + Lambda


## Fan Out Pattern: deliver to multiple SQS

SQS SQS
PUT #1
SDK SDK
SNS
PUT #2 PUT subscribe
PUT #3
Option 1 Option 2 – Fan Out


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


## Amazon EventBridge – Intercept API Calls

User
DeleteTable API Call 💥
alert
Log API call event
DynamoDB Amazon
CloudTrail SNS
EventBridge
(any API call)


## API Gateway – AWS Service Integration

Kinesis Data Streams example
store .json
requests send records files
API Gateway Kinesis Data Kinesis Data Amazon S3
Client
Streams Firehose


## Caching Strategies

Redis
Memcached
DAX
CloudFront API Gateway App logic
Client
EC2 / Lambda
Database
S3
CloudFront (edge)
Caching, TTL, Network, Computation, Cost, Latency


## Blocking an IP address

VPC
Public Subnet
Security Group (allow rules)
NACL
Client EC2 Instance
Deny + Allow rules
public IP + Firewall Software (optional)


## Blocking an IP address – with an ALB

VPC
Public Subnet Private Subnet
ALB Security Group EC2 Security Group
Client NACL Application Load Balancer EC2 Instance
Connection Termination Private IP


## Blocking an IP address – with an NLB

VPC
Public Subnet Private Subnet
NLB Security Group EC2 Security Group
NACL
Client Network Load Balancer EC2 Instance
Private IP


## Blocking an IP address – ALB + WAF

VPC
Public Subnet Private Subnet
ALB Security Group EC2 Security Group
NACL
Client Application Load Balancer EC2 Instance
Private IP
IP Address Filtering
AWS WAF


## Blocking an IP address – ALB, CloudFront & WAF

VPC
Public Subnet Private Subnet
ALB Security Group EC2 Security Group
CloudFront
Public IPs
NACL
Client Application Load Balancer EC2 Instance
CloudFront
NOT helpful Public Private IP
(Geo Restriction)
AWS WAF
(IP Address Filtering)


## High Performance Computing (HPC)

- The cloud is the perfect place to perform HPC
- You can create a very high number of resources in no time
- You can speed up time to results by adding more resources
- You can pay only for the systems you have used
- Perform genomics, computational chemistry, financial risk modeling,

weather prediction, machine learning, deep learning, autonomous driving
- Which services help perform HPC?


## Data Management & Transfer

- AWS Direct Connect:
- Move GB/s of data to the cloud, over a private secure network
- Snowball & Snowmobile
- Move PB of data to the cloud
- AWS DataSync
- Move large amount of data between on-premises and S3, EFS, FSx for Windows


## Compute and Networking

- EC2 Instances:
- CPU optimized, GPU optimized
- Spot Instances / Spot Fleets for cost savings + Auto Scaling
- EC2 Placement Groups: Cluster for good network performance
    - EC2 EC2 EC2
Placement group
Cluster
Same Rack
Same AZ Low latency
10Gbps network
EC2 EC2 EC2


## Compute and Networking

- EC2 Enhanced Networking (SR-IOV)
- Higher bandwidth, higher PPS (packet per second), lower latency
- Option 1: Elastic Network Adapter (ENA) up to 100 Gbps
- Option 2: Intel 82599 VF up to 10 Gbps – LEGACY
- Elastic Fabric Adapter (EFA)
- Improved ENA for HPC, only works for Linux
- Great for inter-node communications, tightly coupled workloads
- Leverages Message Passing Interface (MPI) standard
- Bypasses the underlying Linux OS to provide low-latency, reliable transport


## Storage

- Instance-attached storage:
- EBS: scale up to 256,000 IOPS with io2 Block Express
- Instance Store: scale to millions of IOPS, linked to EC2 instance, low latency
- Network storage:
- Amazon S3: large blob, not a file system
- Amazon EFS: scale IOPS based on total size, or use provisioned IOPS
- Amazon FSx for Lustre:
- HPC optimized distributed file system, millions of IOPS
- Backed by S3


## Automation and Orchestration

- AWS Batch
- AWS Batch supports multi-node parallel jobs, which enables you to run single

jobs that span multiple EC2 instances.
- Easily schedule jobs and launch EC2 instances accordingly
- AWS ParallelCluster
- Open-source cluster management tool to deploy HPC on AWS
- Configure with text files
- Automate creation of VPC, Subnet, cluster type and instance types
- Ability to enable EFA on the cluster (improves network performance)


## Creating a highly available EC2 instance

monitor
Attachment
CloudWatch Event
What time is it?
Public EC2
(or Alarm based on metric)
5:30 pm!
Elastic IP Address
Start the instance
Attach the Elastic IP
Standby EC2 instance


## Creating a highly available EC2 instance

With an Auto Scaling Group
ASG Settings
Auto Scaling group
1 min
Availability Zone 1
1 max
1 desired
>= 2 AZ
What time is it?
EC2 user data to attach
Public EC2 The Elastic IP
5:30 pm!
Elastic IP Address EC2 instance role to
Availability Zone 2
Allow API calls to attach
The Elastic IP
EC2 User Data
Attachment
Based on Tag
Replacement
EC2 instance


## Creating a highly available EC2 instance

With ASG + EBS
Auto Scaling group
Availability Zone 1
EBS Snapshot
On ASG Terminate lifecycle hook
What time is it?
EBS Volume
Public EC2
5:30 pm!
Elastic IP Address EBS Snapshot
Availability Zone 2
+ tags
EC2 User Data
Attachment
Based on Tag
EBS Volume created + attached
EBS
On ASG Launch lifecycle hook
Replacement
EC2 instance


## Other Services

Overview of Services that might come up in a few questions


## What is CloudFormation

- CloudFormation is a declarative way of outlining your AWS

Infrastructure, for any resources (most of them are supported).
- For example, within a CloudFormation template, you say:
- I want a security group
- I want two EC2 instances using this security group
- I want an S3 bucket
- I want a load balancer (ELB) in front of these machines
- Then CloudFormation creates those for you, in the right order, with the
    - exact configuration that you specify


## Benefits of AWS CloudFormation (1/2)

- Infrastructure as code
- No resources are manually created, which is excellent for control
- Changes to the infrastructure are reviewed through code
- Cost
- Each resources within the stack is tagged with an identifier so you can easily see how
    - much a stack costs you
- You can estimate the costs of your resources using the CloudFormation template
- Savings strategy: In Dev, you could automation deletion of templates at 5 PM and
    - recreated at 8 AM, safely


## Benefits of AWS CloudFormation (2/2)

- Productivity
- Ability to destroy and re-create an infrastructure on the cloud on the fly
- Automated generation of Diagram for your templates!
- Declarative programming (no need to figure out ordering and orchestration)
- Don’t re-invent the wheel
- Leverage existing templates on the web!
- Leverage the documentation
- Supports (almost) all AWS resources:
- Everything we’ll see in this course is supported
- You can use “custom resources” for resources that are not supported


## CloudFormation + Infrastructure Composer

- Example: WordPress CloudFormation Stack
    - +
CloudFormation Infrastructure
- We can see all the resources
    - Composer
- We can see the relations between the components


## CloudFormation – Service Role

Permissions
- cloudformation:* User
- iam:PassRole
- IAM role that allows CloudFormation to
    - create/update/delete stack resources on your
behalf
Template
- Give ability to users to create/update/delete the
    - stack resources even if they don’t have
Service Role
permissions to work with the resources in the
- s3:*Bucket
    - stack
- Use cases: CloudFormation
- You want to achieve the least privilege principle
- But you don’t want to give the user all the required
    - permissions to create the stack resources
- User must have iam:PassRole permissions
    - S3 bucket
Stack


## Amazon Simple Email Service (Amazon SES)

- Fully managed service to send emails securely, globally and at scale
- Allows inbound/outbound emails
    - Users
- Reputation dashboard, performance insights, anti-spam feedback
- Provides statistics such as email deliveries, bounces, feedback loop
    - bulk emails
results, email open
- Supports DomainKeys Identified Mail (DKIM) and Sender Policy
    - Framework (SPF)
- Flexible IP deployment: shared, dedicated, and customer-owned IPs
    - Amazon SES
- Send emails using your application using AWS Console, APIs, or SMTP
    - APIs
or SMTP
- Use cases: transactional, marketing and bulk email communications
    - Application


## Amazon Pinpoint

- Scalable 2-way (outbound/inbound) marketing
    - communications service
- Supports email, SMS, push, voice, and in-app messaging
    - Amazon
- Ability to segment and personalize messages with the
    - Pinpoint
SMS
right content to customers
Customers
- Possibility to receive replies
- Scales to billions of messages per day stream events
    - (e.g., TEXT_SUCCESS,
- Use cases: run campaigns by sending marketing, bulk,
    - TEXT_DELIVERED, …)
transactional SMS messages
- Versus Amazon SNS or Amazon SES
- In SNS & SES you managed each message's audience,
    - content, and delivery schedule
- In Amazon Pinpoint, you create message templates,
    - delivery schedules, highly-targeted segments, and full
campaigns
SNS Kinesis Data CloudWatch
Firehose Logs


## Systems Manager – SSM Session Manager

- Allows you to start a secure shell on your EC2 and
    - EC2 Instance
on-premises servers
(SSM Agent)
Execute
- No SSH access, bastion hosts, or SSH keys needed commands
- No port 22 needed (better security)
    - Session
Manager
- Supports Linux, macOS, and Windows
- Send session log data to S3 or CloudWatch Logs IAM
    - Permissions
User


## Systems Manager – Run Command

- Execute a document (= script) or just run a
    - command
- Run command across multiple instances
    - (using resource groups) Amazon SNS
n
o
t EventBridge
if
ic
- No need for SSH a
    - t
io
n
trigger
- Command Output can be shown in the AWS
    - Console, sent to S3 bucket or CloudWatch
Logs Amazon S3 output
- Send notifications to SNS about command
    - Run Command
status (In progress, Success, Failed, …)
- Integrated with IAM & CloudTrail
    - CloudWatch
- Can be invoked using EventBridge Logs
    - EC2 Instances EC2 Instances
(with SSM Agent) (with SSM Agent)


## Systems Manager – Patch Manager

- Automates the process of patching managed
    - instances
- OS updates, applications updates, security
    - AWS Console AWS SDK Maintenance
updates Windows
- Supports EC2 instances and on-premises
    - run
servers AWS-RunBatchBaseline
- Supports Linux, macOS, and Windows
- Patch on-demand or on a schedule using
    - Run Command
Maintenance Windows
- Scan instances and generate patch compliance
    - report (missing patches)
EC2 Instances EC2 Instances
(with SSM Agent) (with SSM Agent)


## Systems Manager – Maintenance Windows

- Defines a schedule for when to perform actions on your instances
- Example: OS patching, updating drivers, installing software, …
- Maintenance Window contains
- Schedule
- Duration
- Set of registered instances
- Set of registered tasks
    - EC2 Instances
trigger every 24 hour update (with SSM Agent)
Maintenance Windows Run Command
EC2 Instances
(with SSM Agent)


## Systems Manager - Automation

- Simplifies common maintenance and
    - deployment tasks of EC2 instances and other
AWS resources
AWS Console AWS SDK Maintenance Amazon AWS Config
- Examples: restart instances, create an AMI,
    - Windows EventBridge Remediation
EBS snapshot
execute automation
- Automation Runbook – SSM Documents to
    - (AWS-RestartEC2Instance)
define actions preformed on your EC2
instances or AWS resources (pre-defined or
custom)
Runbooks SSM Automation
- Can be triggered using:
    - (automation documents)
- Manually using AWS Console, AWS CLI or SDK
    - execute
- Amazon EventBridge
- On a schedule using Maintenance Windows
    - AWS Resources
- By AWS Config for rules remediations
    - …
EC2 Instances
EBS AMI RDS


## Cost Explorer

- Visualize, understand, and manage your AWS costs and usage over time
- Create custom reports that analyze cost and usage data.
- Analyze your data at a high level: total costs and usage across all accounts
- Or Monthly, hourly, resource level granularity
- Choose an optimal Savings Plan (to lower prices on your bill)
- Forecast usage up to 18 months based on previous usage


## Cost Explorer – Monthly Cost by AWS Service



## Cost Explorer– Hourly & Resource Level



## Cost Explorer – Savings Plan

Alternative to Reser ved Instances


## Cost Explorer – Forecast Usage



## AWS Cost Anomaly Detection

- Continuously monitor your cost and usage using ML to detect unusual spends
- It learns your unique, historic spend patterns to detect one-time cost spike

and/or continuous cost increases (you don’t need to define thresholds)
- Monitor AWS services, member accounts, cost allocation tags, or cost categories
- Sends you the anomaly detection report with root-cause analysis
- Get notified with individual alerts or daily/weekly summary (using SNS)

AWS Cost Anomaly Create Cost Monitor Get Alerted Analyze Root Cause
Detection Identify unusual spend at Receive alerts when Analyze the root cause
reduce cost surprises the granularity level unusual spend is detected behind the anomaly and
with Machine Learning that you specify the impact on your costs


## AWS Outposts

- Hybrid Cloud: businesses that keep an on-
    - premises infrastructure alongside a cloud
infrastructure
- Therefore, two ways of dealing with IT systems:
    - AWS Corporate
- One for the AWS cloud (using the AWS console,
    - Cloud data center
CLI, and AWS APIs)
- One for their on-premises infrastructure
    - On-prem
servers
- AWS Outposts are “server racks” that offers the
    - same AWS infrastructure, services, APIs & tools
to build your own applications on-premises just as
Extension of Outposts
in the cloud
AWS services Racks
- AWS will setup and manage “Outposts Racks”
    - within your on-premises infrastructure and you
can start leveraging AWS services on-premises
- You are responsible for the Outposts Rack
    - physical security


## AWS Outposts

- Benefits:
- Low-latency access to on-premises systems
- Local data processing
- Data residency
- Easier migration from on-premises to the cloud
- Fully managed service
- Some services that work on Outposts:

Amazon EC2 Amazon EBS Amazon S3 Amazon EKS Amazon ECS Amazon RDS Amazon EMR


## AWS Batch

- Fully managed batch processing at any scale
- Efficiently run 100,000s of computing batch jobs on AWS
- A “batch” job is a job with a start and an end (opposed to continuous)
- Batch will dynamically launch EC2 instances or Spot Instances
- AWS Batch provisions the right amount of compute / memory
- You submit or schedule batch jobs and AWS Batch does the rest!
- Batch jobs are defined as Docker images and run on ECS
- Helpful for cost optimizations and focusing less on the infrastructure


## AWS Batch – Simplified Example

AWS Batch
EC2 Instance
Insert
ECS
Amazon S3
processed object
Trigger
Spot Instance
Amazon S3


## Batch vs Lambda

- Lambda:
- Time limit
- Limited runtimes
- Limited temporary disk space
- Serverless
- Batch:
- No time limit
- Any runtime as long as it’s packaged as a Docker image
- Rely on EBS / instance store for disk space
- Relies on EC2 (can be managed by AWS)


## Amazon AppFlow

- Fully managed integration service that enables you to securely transfer

data between Software-as-a-Service (SaaS) applications and AWS
- Sources: Salesforce, SAP, Zendesk, Slack, and ServiceNow
- Destinations: AWS services like Amazon S3, Amazon Redshift or non-
    - AWS such as SnowFlake and Salesforce
- Frequency: on a schedule, in response to events, or on demand
- Data transformation capabilities like filtering and validation
- Encrypted over the public internet or privately over AWS PrivateLink
- Don’t spend time writing the integrations and leverage APIs immediately


## Amazon AppFlow

Sources Destinations
Amazon
AppFlow


## AWS Amplify - web and mobile applications

- A set of tools and services that helps you develop and deploy scalable full stack web and mobile
    - applications
- Authentication, Storage, API (REST, GraphQL), CI/CD, PubSub, Analytics, AI/ML Predictions,
    - Monitoring, …
- Connect your source code from GitHub, AWS CodeCommit, Bitbucket, GitLab, or upload directly
    - connect frontend to backend configure backend
using using
Amplify Frontend Libraries Amplify CLI
Amazon S3 Amazon Cognito AWS API
build using Amplify Console …
AppSync Gateway
& deploy
Amazon Amazon Lex Lambda DynamoDB
… … SageMaker
Frontend Amplify backend
Amplify Amazon
Console CloudFront


## Instance Scheduler on AWS

- AWS solution deployed through CloudFormation
    - (not a service)
- Automatically start/stop your AWS services to reduce
    - costs (up to 70%)
- Example: stop company’s EC2 instances outside
    - business hours
- Supports EC2 instances, EC2 Auto Scaling Groups,
    - and RDS instances
- Schedules are managed in a DynamoDB table
- Uses resources’ tags and Lambda to stop/start
    - instances
- Supports cross-account and cross-region resources
- https://aws.amazon.com/solutions/implementations/ins
    - tance-scheduler-on-aws/


## White Papers & Architectures

Well Architected Framework, Disaster Recovery, etc…


## Section Overview

- Well Architected Framework Whitepaper
- Well Architected Tool
- AWS Trusted Advisor
- Reference architectures resources (for real-world)
- Disaster Recovery on AWS Whitepaper


## Well Architected Framework

General Guiding Principles
- https://aws.amazon.com/architecture/well-architected
- Stop guessing your capacity needs
- Test systems at production scale
- Automate to make architectural experimentation easier
- Allow for evolutionary architectures
- Design based on changing requirements
- Drive architectures using data
- Improve through game days
- Simulate applications for flash sale days


## Well Architected Framework

6 Pillars
- 1) Operational Excellence
- 2) Security
- 3) Reliability
- 4) Performance Efficiency
- 5) Cost Optimization
- 6) Sustainability
- They are not something to balance, or trade-offs, they’re a synergy


## AWS Well-Architected Tool

- Free tool to review your architectures against the 6 pillars Well-Architected
    - Framework and adopt architectural best practices
- How does it work?
- Select your workload and answer questions
- Review your answers against the 6 pillars
- Obtain advice: get videos and documentations, generate a report, see the results in a dashboard
- Let’s have a look: https://console.aws.amazon.com/wellarchitected

https://aws.amazon.com/blogs/aws/new-aws-well-architected-tool-review-workloads-against-best-practices/


## Trusted Advisor

- No need to install anything – high level
    - AWS account assessment
- Analyze your AWS accounts and provides
    - recommendation on 6 categories:
- Cost optimization
- Performance
- Security
- Fault tolerance
- Service limits
- Operational Excellence
- Business & Enterprise Support plan
- Full Set of Checks
- Programmatic Access using AWS Support API


## More Architecture Examples

- We’ve explored the most important architectural patterns:
- Classic: EC2, ELB, RDS, ElastiCache, etc…
- Serverless: S3, Lambda, DynamoDB, CloudFront, API Gateway, etc…
- If you want to see more AWS architectures:
- https://aws.amazon.com/architecture/
- https://aws.amazon.com/solutions/


## Exam Review & Tips



## State of learning checkpoint

- Let’s look how far we’ve gone on our learning journey
- https://aws.amazon.com/certification/certified-solutions-architect-
    - associate/


## Practice makes perfect

- If you’re new to AWS, take a bit of AWS practice thanks to this course
    - before rushing to the exam
- The exam recommends you to have one or more years of hands-on
    - experience on AWS
- Practice makes perfect!
- If you feel overwhelmed by the amount of knowledge you just learned,
    - just go through it one more time


## Proceed by elimination

- Most questions are going to be scenario based
- For all the questions, rule out answers that you know for sure are wrong
- For the remaining answers, understand which one makes the most sense
- There are very few trick questions
- Don’t over-think it
- If a solution seems feasible but highly complicated, it’s probably wrong


## Skim the AWS Whitepapers

- You can read about some AWS White Papers here:
- Architecting for the Cloud: AWS Best Practices
- AWS Well-Architected Framework
- AWS Disaster Recovery (https://aws.amazon.com/disaster-recovery/)
- Overall we’ve explored all the most important concepts in the course
- It’s never bad to have a look at the whitepapers you think are
    - interesting!


## Read each service’s FAQ

- FAQ = Frequently asked questions
- Example: https://aws.amazon.com/vpc/faqs/
- FAQ cover a lot of the questions asked at the exam
- They help confirm your understanding of a service


## Get into the AWS Community

- Help out and discuss with other people in the course Q&A
- Review questions asked by other people in the Q&A
- Do the practice test in this section
- Read forums online
- Read online blogs
- Attend local meetups and discuss with other AWS engineers
- Watch re-invent videos on Youtube (AWS Conference)


## How will the exam work?

- You’ll have to register online at https://www.aws.training/
- Fee for the exam is 150 USD
- Provide one identity documents (ID, Passport, details are in emails sent to you…)
- No notes are allowed, no pen is allowed, no speaking
- 65 questions will be asked in 130 minutes
- Use the “Flag” feature to mark questions you want to re-visit
- At the end you can optionally review all the questions / answers
- To pass you need a score of a least 720 out of 1000
- You will know within 5 days if you passed / failed the exams (most of the time less)
- You will know the overall score a few days later (email notification)
- You will not know which answers were right / wrong
- If you fail, you can retake the exam again 14 days later


## Your AWS Cer tification journey

Foundational Professional
Knowledge-based certification for Role-based certifications that validate advanced skills
foundational understanding of AWS Cloud. and knowledge required to design secure, optimized,
No prior experience needed. and modernized applications and to automate processes on AWS.
2 years of prior AWS Cloud experience recommended.
Associate Specialty
Role-based certifications that showcase your knowledge Dive deeper and position yourself as a trusted advisor to your
and skills on AWS and build your credibility as an AWS Cloud professional. stakeholders and/or customers in these strategic areas.
Prior cloud and/or strong on-premises IT experience recommended. Refer to the exam guides on the exam pages for recommended experience.


## AWS Cer tification Paths – Architecture

Architecture
Solutions Architect
Design, develop, and manage
cloud infrastructure and assets,
work with DevOps to migrate
applications to the cloud
optional for IT/ recommended for IT/cloud Dive Deep
cloud professionals professionals to leverage AI
Architecture
Application Architect
Design significant aspects of
application architecture including
user interface, middleware, and
infrastructure, and ensure
enterprise-wide scalable, reliable,
and manageable systems optional for IT/ recommended for IT/cloud Dive Deep
cloud professionals professionals to leverage AI
https://d1.awsstatic.com/training-and-
certification/docs/AWS_certification_paths.pdf


## AWS Cer tification Paths – Operations

Operations
Systems Administrator
Install, upgrade, and maintain
computer components and
software, and integrate
automation processes
optional for IT/ Dive Deep
cloud professionals
Operations
Cloud Engineer
Implement and operate an
organization’s networked computing
infrastructure and Implement
security systems to maintain
data safety
optional for IT/ Dive Deep
cloud professionals


## AWS Cer tification Paths – DevOps

DevOps
Test Engineer
Embed testing and quality
best practices for software
development from design to release,
throughout the product life cycle
optional for IT/
cloud professionals
DevOps
Cloud DevOps Engineer
Design, deployment, and operations
of large-scale global hybrid
cloud computing environment,
advocating for end-to-end
automated CI/CD DevOps pipelines
optional for IT/ Optional recommended for IT/cloud Dive Deep
cloud professionals professionals working on
DevOps AI/ML projects
DevSecOps Engineer
Accelerate enterprise cloud adoption
while enabling rapid and stable delivery
of capabilities using CI/CD principles,
methodologies, and technologies
optional for IT/ recommended for IT/cloud
cloud professionals professionals working on
AI/ML projects


## AWS Cer tification Paths – Security

Security
Cloud Security Engineer
Design computer security architecture
and develop detailed cyber security designs.
Develop, execute, and track performance
of security measures to protect information
optional for IT/ recommended for IT/cloud Dive Deep
cloud professionals professionals to secure
AI/ML systems
Security
Cloud Security Architect
Design and implement enterprise cloud
solutions applying governance to identify,
communicate, and minimize business and
technical risks
optional for IT/ recommended for IT/cloud Dive Deep
cloud professionals professionals to secure
AI/ML systems


## AWS Cer tification Paths – Development &

Networking
Development
Software Development Engineer
Develop, construct, and maintain
software across platforms and devices
optional for IT/ recommended for IT/cloud
cloud professionals professionals to leverage AI
Networking
Network Engineer
Design and implement computer
and information networks, such as
local area networks (LAN),
wide area networks (WAN),
optional for IT/ Dive Deep
intranets, extranets, etc.
cloud professionals


## AWS Cer tification Paths – Data Analytics &

AI/ML
Data Analytics
Cloud Data Engineer
Automate collection and processing
of structured/semi-structured data
and monitor data pipeline performance
optional for IT/ recommended for IT/cloud Dive Deep
cloud professionals professionals working on
AI/ML projects
AI/ML
Machine Learning Engineer
Research, build, and design artificial
intelligence (AI) systems to automate
predictive models, and design machine
learning systems, models, and schemes
optional for IT/ optional for AI/ML Dive Deep
cloud professionals professionals


## AWS Cer tification Paths – AI/ML

AI/ML
Prompt Engineer
Design, test, and refine text
prompts to optimize the
performance of AI language models
optional for IT/ Dive Deep
cloud professionals
AI/ML
Machine Learning Ops Engineer
Build and maintain AI and ML platforms
and infrastructure. Design, implement,
and operationally support AI/ML model
activity and deployment infrastructure
optional for IT/ optional for AI/ML
cloud professionals professionals
AI/ML
Data Scientist
Develop and maintain AI/ML models
to solve business problems. Train and
fine tune models and evaluate
their performance
optional for IT/ optional for AI/ML
cloud professionals professionals


## Congratulations!

