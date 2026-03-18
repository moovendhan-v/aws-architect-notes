# Containers on AWS


## What is Docker?

- Docker is a software development platform to deploy apps
- Apps are packaged in containers that can be run on any OS
- Apps run the same, regardless of where they’re run
- Any machine
- No compatibility issues
- Predictable behavior
- Less work
- Easier to maintain and deploy
- Works with any language, any OS, any technology
- Use cases: microservices architecture, lift-and-shift apps from on-
    - premises to the AWS cloud, …


## Docker on an OS

Server (e.g., EC2 instance)


## Where are Docker images stored?

- Docker images are stored in Docker Repositories
- Docker Hub (https://hub.docker.com)
- Public repository
- Find base images for many technologies or OS (e.g., Ubuntu, MySQL, …)
- Amazon ECR (Amazon Elastic Container Registry)
- Private repository
- Public repository (Amazon ECR Public Gallery https://gallery.ecr.aws)


## Docker vs. Vir tual Machines

- Docker is ”sort of ” a virtualization technology, but not exactly
- Resources are shared with the host => many containers on one server
    - Apps Apps Apps
Guest OS Guest OS Guest OS
(VM) (VM) (VM)
Hypervisor Docker Daemon
Host OS Host OS (EC2 Instance)
Infrastructure Infrastructure


## Getting Star ted with Docker

Build Run
container
Dockerfile
image
Push Pull
Docker Repository
Amazon
ECR


## Docker Containers Management on AWS

- Amazon Elastic Container Service (Amazon ECS)
    - Amazon ECS
- Amazon’s own container platform
- Amazon Elastic Kubernetes Service (Amazon EKS)
    - Amazon EKS
- Amazon’s managed Kubernetes (open source)
- AWS Fargate
    - AWS Fargate
- Amazon’s own Serverless container platform
- Works with ECS and with EKS
- Amazon ECR:
    - Amazon ECR
- Store container images


## Amazon ECS - EC2 Launch Type

Amazon ECS / ECS Cluster
- ECS = Elastic Container Service
    - New Docker
- Launch Docker containers on AWS =
    - Container
Launch ECS Tasks on ECS Clusters
- EC2 Launch Type: you must provision
    - & maintain the infrastructure (the EC2
EC2 Instance EC2 Instance EC2 Instance
instances)
- Each EC2 Instance must run the ECS
    - Agent to register in the ECS Cluster
- AWS takes care of starting / stopping
    - containers
ECS Agent ECS Agent ECS Agent


## Amazon ECS – Fargate Launch Type

- Launch Docker containers on AWS
    - New Docker
- You do not provision the infrastructure Container
    - (no EC2 instances to manage)
- It’s all Serverless!
    - AWS Fargate / ECS Cluster
- You just create task definitions
- AWS just runs ECS Tasks for you based
    - on the CPU / RAM you need
- To scale, just increase the number of
    - tasks. Simple - no more EC2 instances


## Amazon ECS – IAM Roles for ECS

EC2 Instance Profile
- EC2 Instance Profile (EC2 Launch Type only):
    - ECS
EC2 Instance
- Used by the ECS agent
- Makes API calls to ECS service
    - ECR
- Send container logs to CloudWatch Logs
- Pull Docker image from ECR ECS Agent CloudWatch
    - Logs
- Reference sensitive data in Secrets Manager or
    - SSM Parameter Store ECS Task A Role
Task A S3
- ECS Task Role:
- Allows each task to have a specific role ECS Task B Role
- Use different roles for the different ECS Services
    - Task B DynamoDB
you run
- Task Role is defined in the task definition


## Amazon ECS – Load Balancer Integrations

- Application Load Balancer supported
    - EC2 Instance
and works for most use cases
ECS Task
- Network Load Balancer recommended
    - ECS Task
only for high throughput / high
performance use cases, or to pair it with
80/443
AWS Private Link
EC2 Instance
Users
Application
ECS Task
Load Balancer
- Classic Load Balancer supported but
    - not recommended (no advanced ECS Task
features – no Fargate)
ECS Cluster


## Amazon ECS – Data Volumes (EFS)

- Mount EFS file systems onto ECS tasks
- Works for both EC2 and Fargate launch types
    - EC2 Instance Fargate
- Tasks running in any AZ will share the same data
    - in the EFS file system
- Fargate + EFS = Serverless
- Use cases: persistent multi-AZ shared storage for
    - mount mount
your containers
ECS Cluster
- Note:
    - Amazon EFS
- Amazon S3 cannot be mounted as a file system
    - File System


## ECS Service Auto Scaling

- Automatically increase/decrease the desired number of ECS tasks
- Amazon ECS Auto Scaling uses AWS Application Auto Scaling
- ECS Service Average CPU Utilization
- ECS Service Average Memory Utilization - Scale on RAM
- ALB Request Count Per Target – metric coming from the ALB
- Target Tracking – scale based on target value for a specific CloudWatch metric
- Step Scaling – scale based on a specified CloudWatch Alarm
- Scheduled Scaling – scale based on a specified date/time (predictable changes)
- ECS Service Auto Scaling (task level) ≠ EC2 Auto Scaling (EC2 instance level)
- Fargate Auto Scaling is much easier to setup (because Serverless)


## EC2 Launch Type – Auto Scaling EC2 Instances

- Accommodate ECS Service Scaling by adding underlying EC2 Instances
- Auto Scaling Group Scaling
- Scale your ASG based on CPU Utilization
- Add EC2 instances over time
- ECS Cluster Capacity Provider
- Used to automatically provision and scale the infrastructure for your ECS Tasks
- Capacity Provider paired with an Auto Scaling Group
- Add EC2 Instances when you’re missing capacity (CPU, RAM…)


## ECS Scaling – Service CPU Usage Example

CPU
Usage
Task 1 Task 3
(new)
Task 2
Service A
Auto Scaling
Auto Scaling Group e
al
c
S
Scale ECS Capacity Providers
(optional)
CloudWatch Metric Trigger
(ECS Service CPU Usage)
CloudWatch Alarm


## ECS tasks invoked by Event Bridge

Region
VPC
Upload object
AWS Fargate
G
e
Client t
o
S3 Bucket b
je
c
t
Task
(new)
ECS Task Role
Event
(Access S3 & DynamoDB) Save result
CS
Task
n
E
u
R
ule:
R
Amazon
DynamoDB
Amazon ECS Cluster
Amazon
EventBridge


## ECS tasks invoked by Event Bridge Schedule

AWS Fargate
Task
(new) ECS Task Role
Every 1 hour Access S3
Rule: Run ECS Task Batch Processing
Amazon S3
Amazon
EventBridge
Amazon ECS Cluster


## ECS – SQS Queue Example

Task 1 Task 3
Messages Poll for messages
SQS Queue
Task 2
Service A
ECS Service Auto Scaling


## ECS – Intercept Stopped Tasks using EventBridge

ECS Task
event
trigger email
exited
EventBridge SNS Administrator
Containers
Event Pattern


## Amazon ECR

ECR Repository
- ECR = Elastic Container Registry
    - Docker Docker
- Store and manage Docker images on AWS
    - Image A Image B
- Private and Public repository (Amazon ECR
    - Public Gallery https://gallery.ecr.aws)
pull pull
- Fully integrated with ECS, backed by Amazon S3 IAM Role
- Access is controlled through IAM (permission
    - EC2 Instance
errors => policy)
- Supports image vulnerability scanning, versioning,
    - image tags, image lifecycle, …
ECS Cluster


## Amazon EKS Overview

- Amazon EKS = Amazon Elastic Kubernetes Service
- It is a way to launch managed Kubernetes clusters on AWS
- Kubernetes is an open-source system for automatic deployment, scaling and
    - management of containerized (usually Docker) application
- It’s an alternative to ECS, similar goal but different API
- EKS supports EC2 if you want to deploy worker nodes or Fargate to deploy
    - serverless containers
- Use case: if your company is already using Kubernetes on-premises or in
    - another cloud, and wants to migrate to AWS using Kubernetes
- Kubernetes is cloud-agnostic (can be used in any cloud – Azure, GCP…)
- For multiple regions, deploy one EKS cluster per region
- Collect logs and metrics using CloudWatch Container Insights


## Amazon EKS - Diagram

AWS Cloud
Availability Zone 1 Availability Zone 2 Availability Zone 3
VPC
Public subnet 1 Public subnet 2 Public subnet 3
EKS
Public
Service LB
NGW NGW NGW
ELB ELB ELB
Private subnet 1 Private subnet 2 Private subnet 3 EKS
Private
Service LB
ELB
EKS node EKS node EKS node
Auto Scaling Group
EKS Pods EKS Pods EKS Pods
EKS Worker Nodes


## Amazon EKS – Node Types

- Managed Node Groups
- Creates and manages Nodes (EC2 instances) for you
- Nodes are part of an ASG managed by EKS
- Supports On-Demand or Spot Instances
- Self-Managed Nodes
- Nodes created by you and registered to the EKS cluster and managed by an ASG
- You can use prebuilt AMI - Amazon EKS Optimized AMI
- Supports On-Demand or Spot Instances
- AWS Fargate
- No maintenance required; no nodes managed


## Amazon EKS – Data Volumes

- Need to specify StorageClass manifest on your EKS cluster
- Leverages a Container Storage Interface (CSI) compliant driver
- Support for…
- Amazon EBS
- Amazon EFS (works with Fargate)
- Amazon FSx for Lustre
- Amazon FSx for NetApp ONTAP


## AWS App Runner

- Fully managed service that makes it easy to deploy web
    - applications and APIs at scale Container Source
Image (Docker) Code
- No infrastructure experience required
- Start with your source code or container image
    - Configure Settings
- Automatically builds and deploy the web app
    - vCPU, RAM,
Auto Scaling,
- Automatic scaling, highly available, load balancer, encryption
    - Health Check
- VPC access support
- Connect to database, cache, and message queue services
    - Create & Deploy
- Use cases: web apps, APIs, microservices, rapid production
    - deployments
Access using URL


## AWS App2Container (A2C)

- CLI tool for migrating and modernizing Java and .NET web apps into
    - Docker Containers
- Lift-and-shift your apps running in on-premises bare metal, virtual
    - machines, or in any Cloud to AWS
- Accelerate modernization, no code changes, migrate legacy apps…
- Generates CloudFormation templates (compute, network…)
- Register generated Docker containers to ECR
- Deploy to ECS, EKS, or App Runner
- Supports pre-built CI/CD pipelines


## AWS App2Container (A2C)

Amazon ECR
(store image)
Discover & Analyze Extract & Containerize Create Deployment Deploy to AWS
Amazon ECS
create app inventory extract an app with Artifacts store Docker image in ECR,
(deploy)
and analyze runtime dependencies and create and deploy to ECS, EKS, or
generate ECS Task and
dependencies a Docker image App Runner
EKS Pod definitions, and
create CI/CD pipelines, and
Amazon EKS
other infrastructure
(deploy)
App Runner
(deploy)
CloudFormation
Template
