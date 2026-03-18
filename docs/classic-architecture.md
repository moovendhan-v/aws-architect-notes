# Classic Solutions Architecture


## Section Introduction

- These solutions architectures are the best part of this course
- Let’s understand how all the technologies we’ve seen work together
- This is a section you need to be 100% comfortable with
- We’ll see the progression of a Solution’s architect mindset through many
    - sample case studies:
- WhatIsTheTime.Com
- MyClothes.Com
- MyWordPress.Com
- Instantiating applications quickly
- Beanstalk


## Stateless Web App: WhatIsTheTime.com

- WhatIsTheTime.com allows people to know what time it is
- We don’t need a database
- We want to start small and can accept downtime
- We want to fully scale vertically and horizontally, no downtime
- Let’s go through the Solutions Architect journey for this app
- Let’s see how we can proceed!


## Stateless web app: What time is it?

Star ting simple
Elastic IP Address
What time is it?
5:30 pm!
User
Public EC2


## Stateless web app: What time is it?

Scaling ver tically
What time is it?
Elastic IP Address
7:30 pm!
What time is it?
Downtime while upgrading to M5
5:30 pm!
User
What time is it? Public EC2
6:30 pm!


## Stateless web app: What time is it?

Scaling horizontally
What time is it?
7:30 pm!
What time is it?
5:30 pm!
User
What time is it?
6:30 pm!


## Stateless web app: What time is it?

Scaling horizontally
DNS Query What time is it?
Public EC2 instance,
For api.whatisthetime.com
No Elastic IP
A Record
TTL 1 hour 7:30 pm!
What time is it?
5:30 pm!
What time is it?
6:30 pm!


## Stateless web app: What time is it?

Scaling horizontally, adding and removing instances
DNS Query What time is it?
For api.whatisthetime.com
INSTANCE IS GONE!
A Record
TTL 1 hour 7:30 pm!
What time is it?
Public EC2 instance,
No Elastic IP
5:30 pm!
What time is it?
6:30 pm!


## Stateless web app: What time is it?

Scaling horizontally, with a load balancer
What time is it?
Availability zone 1 Availability zone 1
Restricted
Security groups rules
DNS Query
ELB +
For api.whatisthetime.com
Health Checks
Alias Record
Private
EC2 instances


## Stateless web app: What time is it?

Scaling horizontally, with an auto-scaling group
What time is it?
Availability zone 1 Availability zone 1
Auto Scaling group
DNS Query
ELB +
For api.whatisthetime.com
Health Checks
Alias Record
Private
EC2 instances


## Stateless web app: What time is it?

Making our app multi-AZ
Auto Scaling group
What time is it?
Availability zone 1 to 3 Availability zone 1
Availability zone 2
DNS Query
ELB +
For api.whatisthetime.com
Health Checks
Alias Record
+ Multi AZ
Availability zone 3


## Minimum 2 AZ => Let’s reser ve capacity

Auto Scaling group
Availability zone 1 to 3 Availability zone 1
Availability zone 2
DNS Query
ELB +
For api.whatisthetime.com
Health Checks
Alias Record
+ Multi AZ
Minimum capacity
= reserved instances
= cost savings


## In this lecture we’ve discussed…

- Public vs Private IP and EC2 instances
- Elastic IP vs Route 53 vs Load Balancers
- Route 53 TTL, A records and Alias Records
- Maintaining EC2 instances manually vs Auto Scaling Groups
- Multi AZ to survive disasters
- ELB Health Checks
- Security Group Rules
- Reservation of capacity for costing savings when possible


## Stateful Web App: MyClothes.com

- MyClothes.com allows people to buy clothes online.
- There’s a shopping cart
- Our website is having hundreds of users at the same time
- We need to scale, maintain horizontal scalability and keep our web
    - application as stateless as possible
- Users should not lose their shopping cart
- Users should have their details (address, etc) in a database
- Let’s see how we can proceed!


## Stateful Web App: MyClothes.com

Auto Scaling group
Availability zone 1
Multi AZ
Availability zone 2
Availability zone 3


## Stateful Web App: MyClothes.com

Introduce Stickiness (Session Affinity)
Auto Scaling group
Availability zone 1
Multi AZ
Availability zone 2
ELB Stickiness
Availability zone 3


## Stateful Web App: MyClothes.com

Introduce User Cookies
Auto Scaling group
Availability zone 1
Multi AZ
Send shopping cart Stateless
content in Web Cookies HTTP requests are heavier
Availability zone 2
Security risk
(cookies can be altered)
Cookies must be validated
Cookies must be less than 4KB
Availability zone 3


## Stateful Web App: MyClothes.com

Introduce Ser ver Session
ElastiCache
Auto Scaling group
Availability zone 1
Multi AZ
Send session_id in
Web Cookies
Store / retrieve
Availability zone 2
session data
Availability zone 3
Amazon DynamoDB
(alternative)


## Stateful Web App: MyClothes.com

Storing User Data in a database
ElastiCache
Auto Scaling group
Availability zone 1
Multi AZ
Availability zone 2
Store / retrieve user data
(address, name, etc)
Availability zone 3
Amazon RDS


## Stateful Web App: MyClothes.com

Scaling Reads
ElastiCache
Auto Scaling group
Availability zone 1
Multi AZ
Availability zone 2 RDS
Master
(writes)
replication
Availability zone 3
RDS
Read Replicas


## Stateful Web App: MyClothes.com

Scaling Reads (Alternative) – Lazy Loading
ElastiCache
Auto Scaling group
Availability zone 1 cache
Multi AZ
hit
Read from cache
Availability zone 2
Read/write
Availability zone 3
RDS


## Stateful Web App: MyClothes.com

Multi AZ – Sur vive disasters
ElastiCache
Multi AZ
Auto Scaling group
Availability zone 1
Multi AZ
Availability zone 2
Availability zone 3
RDS
Multi AZ


## Stateful Web App: MyClothes.com

Security Groups
Restrict traffic to ElastiCache
Security group from the
EC2 security group
Auto Scaling group
Availability zone 1 ElastiCache
Multi AZ
Open HTTP / HTTPS
to 0.0.0.0/0
Availability zone 2
RDS
Availability zone 3
Restrict traffic to RDS
Restrict traffic to EC2
Security group from the
Security group from the LB
EC2 security group


## In this lecture we’ve discussed…

3-tier architectures for web applications
- ELB sticky sessions
- Web clients for storing cookies and making our web app stateless
- ElastiCache
- For storing sessions (alternative: DynamoDB)
- For caching data from RDS
- Multi AZ
- RDS
- For storing user data
- Read replicas for scaling reads
- Multi AZ for disaster recovery
- Tight Security with security groups referencing each other


## Stateful Web App: MyWordPress.com

- We are trying to create a fully scalable WordPress website
- We want that website to access and correctly display picture uploads
- Our user data, and the blog content should be stored in a MySQL database.
- Let’s see how we can achieve this!


## Stateful Web App: MyWordPress.com

RDS layer
Auto Scaling group
Availability zone 1
Multi AZ
Availability zone 2
RDS
Multi AZ
Availability zone 3


## Stateful Web App: MyWordPress.com

Scaling with Aurora: Multi AZ & Read Replicas
Auto Scaling group
Availability zone 1
Multi AZ
Availability zone 2
Aurora MySQL
Multi AZ
Availability zone 3
Read Replicas


## Stateful Web App: MyWordPress.com

Storing images with EBS
Multi AZ
Availability zone 1
Send image
Amazon EBS
Volume


## Stateful Web App: MyWordPress.com

Storing images with EBS
Availability zone 1
Multi AZ
Amazon EBS
Volume
Send image
Availability zone 2
Amazon EBS
Volume


## Stateful Web App: MyWordPress.com

Storing images with EFS
Availability zone 1
Multi AZ
ENI
Send image
Availability zone 2
EFS
ENI


## In this lecture we’ve discussed…

- Aurora Database to have easy Multi-AZ and Read-Replicas
- Storing data in EBS (single instance application)
- Vs Storing data in EFS (distributed application)


## Instantiating Applications quickly

- When launching a full stack (EC2, EBS, RDS), it can take time to:
- Install applications
- Insert initial (or recovery) data
- Configure everything
- Launch the application
- We can take advantage of the cloud to speed that up!


## Instantiating Applications quickly

- EC2 Instances:
- Use a Golden AMI: Install your applications, OS dependencies etc.. beforehand
    - and launch your EC2 instance from the Golden AMI
- Bootstrap using User Data: For dynamic configuration, use User Data scripts
- Hybrid: mix Golden AMI and User Data (Elastic Beanstalk)
- RDS Databases:
- Restore from a snapshot: the database will have schemas and data ready!
- EBS Volumes:
- Restore from a snapshot: the disk will already be formatted and have data!


## Typical architecture: Web App 3-tier

Route 53
ElastiCache
Auto Scaling group
Availability zone 1
Multi AZ
Store / retrieve
session data
Availability zone 2
+ Cached data
ELB
Availability zone 3
Amazon RDS
Read / write data
PUBLIC SUBNET PRIVATE SUBNET DATA SUBNET


## Developer problems on AWS

- Managing infrastructure
- Deploying Code
- Configuring all the databases, load balancers, etc
- Scaling concerns
- Most web apps have the same architecture (ALB + ASG)
- All the developers want is for their code to run!
- Possibly, consistently across different applications and environments


## Elastic Beanstalk – Overview

- Elastic Beanstalk is a developer centric view of deploying an application
    - on AWS
- It uses all the component’s we’ve seen before: EC2, ASG, ELB, RDS, …
- Managed service
- Automatically handles capacity provisioning, load balancing, scaling, application
    - health monitoring, instance configuration, …
- Just the application code is the responsibility of the developer
- We still have full control over the configuration
- Beanstalk is free but you pay for the underlying instances


## Elastic Beanstalk – Components

- Application: collection of Elastic Beanstalk components (environments,
    - versions, configurations, …)
- Application Version: an iteration of your application code
- Environment
- Collection of AWS resources running an application version (only one application
    - version at a time)
- Tiers: Web Server Environment Tier & Worker Environment Tier
- You can create multiple environments (dev, test, prod, …)
    - update version
Create Upload Launch Manage
Application Version Environment Environment
deploy new version


## Elastic Beanstalk – Suppor ted Platforms

- Go • Ruby
- Java SE • Packer Builder
- Java with Tomcat • Single Container Docker
- .NET Core on Linux • Multi-container Docker
- .NET on Windows Server • Preconfigured Docker
- Node.js
- PHP
- Python


## Web Ser ver Tier vs. Worker Tier

Web Environment
Worker Environment
(myapp.us-east-1.elasticbeanstalk.com)
SQS Queue
ELB
Availability Zone 1 Availability Zone 2 Availability Zone 1 Availability Zone 2
SQS message SQS message
pull
messages
Security Group Security Group
Auto Scaling group
Auto Scaling group
EC2 Instance EC2 Instance
EC2 Instance EC2 Instance
(Worker) (Worker)
(Web Server) (Web Server)
- Scale based on the number of SQS messages
- Can push messages to SQS queue from
    - another Web Server Tier


## Elastic Beanstalk Deployment Modes

High Availability with Load Balancer
Single Instance
Great for prod
Great for dev
Availability Zone 1 Availability Zone 1 ALB Availability Zone 2
Elastic IP
Auto Scaling Group
EC2 Instance EC2 Instance EC2 Instance
RDS Master RDS Master RDS Standby
