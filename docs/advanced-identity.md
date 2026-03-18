# Advanced Identity in AWS


## AWS Organizations

- Global service
- Allows to manage multiple AWS accounts
- The main account is the management account
- Other accounts are member accounts
- Member accounts can only be part of one organization
- Consolidated Billing across all accounts - single payment method
- Pricing benefits from aggregated usage (volume discount for EC2, S3…)
- Shared reserved instances and Savings Plans discounts across accounts
- API is available to automate AWS account creation


## AWS Organizations

Root Organizational Unit (OU)
Management Account
OU (Dev) OU (Prod)
OU (HR) OU (Finance)
Member Accounts


## Organizational Units (OU) - Examples

Business Unit Environmental Lifecycle Project-Based
Sales Prod Project 1
Account 1 Account 1 Account 1
Project 1
Sales OU Prod OU
OU
Sales Prod Project 1
Account 2 Account 2 Account 2
Retail Dev Project 2
Account 1 Account 1 Account 1
Management Management Management Project 2
Retail OU Dev OU
Account Account Account OU
Retail Dev Project 2
Account 2 Account 2 Account 2
Finance Test Project 3
Account 1 Account 1 Account 1
Finance Project 3
Test OU
OU OU
Finance Test Project 3
Account 2 Account 2 Account 2


## AWS Organizations

- Advantages
- Multi Account vs One Account Multi VPC
- Use tagging standards for billing purposes
- Enable CloudTrail on all accounts, send logs to central S3 account
- Send CloudWatch Logs to central logging account
- Establish Cross Account Roles for Admin purposes
- Security: Service Control Policies (SCP)
- IAM policies applied to OU or Accounts to restrict Users and Roles
- They do not apply to the management account (full admin power)
- Must have an explicit allow from the root through each OU in the direct path

to the target account (does not allow anything by default – like IAM)


## SCP Hierarchy

FullAWSAccess OU (Root)
- Management Account
- Can do anything (no SCP apply)
- Account A
    - Deny Athena Management Account
- Can do anything
    - FullAWSAccess • EXCEPT S3 (explicit Deny from
Sandbox OU)
FullAWSAccess + Deny S3 OU (Sandbox) OU (Workloads) • EXCEPT EC2 (explicit Deny)
Allow EC2 • Account B & C
- Can do anything
    - OU (Test)
FullAWSAccess + Deny EC2 Account A • EXCEPT S3 (explicit Deny from
Sandbox OU)
Account D
- Account D
    - Account B • Can access EC2
OU (Prod)
- Prod OU & Account E & F
    - FullAWSAccess
Account E • Can do anything
Account C
Account F


## SCP Examples

Blocklist and Allowlist strategies
More examples: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_example-scps.html


## AWS Organizations – Tag Policies

- Helps you standardize tags across resources in an
    - AWS Organization
- Ensure consistent tags, audit tagged resources,
    - maintain proper resources categorization, …
- You define tag keys and their allowed values
- Helps with AWS Cost Allocation Tags and
    - Attribute-based Access Control
- Prevent any non-compliant tagging operations on
    - specified services and resources (has no effect
on resources without tags)
- Generate a report that lists all tagged/non-
    - compliant resources
- Use EventBridge to monitor non-compliant tags


## IAM Conditions

aws:SourceIp aws:RequestedRegion
restrict the client IP from restrict the region the
which the API calls are being made API calls are made to


## IAM Conditions

ec2:ResourceTag aws:MultiFactorAuthPresent
restrict based on tags to force MFA


## IAM for S3

- s3:ListBucket permission applies to
    - arn:aws:s3:::test
- => bucket level permission
- s3:GetObject, s3:PutObject,
    - s3:DeleteObject applies to
arn:awn:s3:::test/*
- => object level permission


## Resource Policies & aws:PrincipalOrgID

- aws:PrincipalOrgID can be used in any resource policies to restrict
    - access to accounts that are member of an AWS Organization
AWS Organization
(o-yyyyyyyyyy)
…
Member Accounts
S3 Bucket
(2022-financial-data)
User outside Organization


## IAM Roles vs Resource Based Policies

- Cross account:
- attaching a resource-based policy to a resource (example: S3 bucket policy)
- OR using a role as a proxy
    - User Role
Account A Account B
Amazon S3
User S3 Bucket
Account A Policy
Amazon S3


## IAM Roles vs Resource-Based Policies

- When you assume a role (user, application or service), you give up your

original permissions and take the permissions assigned to the role
- When using a resource-based policy, the principal doesn’t have to give up his
    - permissions
- Example: User in account A needs to scan a DynamoDB table in Account A

and dump it in an S3 bucket in Account B.
- Supported by: Amazon S3 buckets, SNS topics, SQS queues, etc…


## Amazon EventBridge – Security

- When a rule runs, it needs
    - permissions on the target
- Resource-based policy: Lambda,
    - EventBridge
Lambda with
SNS, SQS, S3 buckets, API
Rule
Resource based Policy
Gateway…
e.g. Allow EventBridge
- IAM role: EC2 Auto Scaling, IAM Role
    - Systems Manager Run
Command, ECS task…
EventBridge EC2 Auto Scaling
Rule


## IAM Permission Boundaries

- IAM Permission Boundaries are supported for users and roles (not groups)
- Advanced feature to use a managed policy to set the maximum permissions

an IAM entity can get.
+ =
Example: No Permissions
IAM Permission Boundary IAM Permissions
Through IAM Policy


## IAM Permission Boundaries

- Can be used in combinations of Use cases
    - AWS Organizations SCP
- Delegate responsibilities to non
    - administrators within their permission
boundaries, for example create new IAM
users
- Allow developers to self-assign policies
    - and manage their own permissions, while
making sure they can’t “escalate” their
privileges (= make themselves admin)
- Useful to restrict one specific user
    - (instead of a whole account using
Organizations & SCP)
https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html


## IAM Policy Evaluation Logic

https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html


## Example IAM Policy

- Can you perform sqs:CreateQueue?
- Can you perform sqs:DeleteQueue?
- Can you perform
    - ec2:DescribeInstances?


## AWS IAM Identity Center

(successor to AWS Single Sign-On)
- One login (single sign-on) for all your
- AWS accounts in AWS Organizations
- Business cloud applications (e.g., Salesforce, Box, Microsoft 365, …)
- SAML2.0-enabled applications
- EC2 Windows Instances
- Identity providers
- Built-in identity store in IAM Identity Center
- 3rd party: Active Directory (AD), OneLogin, Okta…


## AWS IAM Identity Center – Login Flow

AWS IAM Identity Center


## AWS IAM Identity Center

AWS Cloud
AWS IAM Identity Center
AWS
Windows
SSO
login Organization
EC2
Permission Sets Business Cloud Apps
Browser Interface
Store / retrieve
User identities
Active Directory
IAM Identity Center
Custom SAML2.0-enabled Apps
Users & groups
Built-in Identity Store
(On-premises, cloud)


## IAM Identity Center

AWS Organization
IAM Identity Center
(in Management account)
Management Account
Group (Developers)
OU (Development) OU (Production)
Bob Alice
Dev Account A Prod Account A
assign assign
Dev Account B Prod Account B
Permission Set
ReadOnlyAccess
Permission Set
FullAccess


## AWS IAM Identity Center

Fine-grained Permissions and Assignments
- Multi-Account Permissions
- Manage access across AWS accounts in your AWS Organization
    - AWS Organization
- Permission Sets – a collection of one or more IAM Policies
    - assigned to users and groups to define AWS access
Dev Prod
Account Account
- Application Assignments
- SSO access to many SAML 2.0 business applications (Salesforce,
    - Box, Microsoft 365, …) RDS Aurora RDS Aurora
- Provide required URLs, certificates, and metadata
    - IAM Role IAM Role
- Attribute-Based Access Control (ABAC)
    - assume
- Fine-grained permissions based on users’ attributes stored in Permission Sets
    - IAM Identity Center Identity Store (DB Admins)
- Example: cost center, title, locale, …
    - Permission Sets
- Use case: Define permissions once, then modify AWS access by
    - (DB Admins)
changing the attributes
Database
IAM Identity Center
Admins


## What is Microsoft Active Director y (AD)?

- Found on any Windows Server
    - with AD Domain Services
Domain Controller
- Database of objects: User
    - John
Accounts, Computers, Printers,
Password
File Shares, Security Groups
- Centralized security
    - management, create account,
assign permissions
- Objects are organized in trees
- A group of trees is a forest


## AWS Director y Services

- AWS Managed Microsoft AD
    - auth trust auth
- Create your own AD in AWS, manage users
    - locally, supports MFA
- Establish “trust” connections with your on-
    - premises AD
On-prem AD AWS Managed AD
- AD Connector
    - proxy auth
- Directory Gateway (proxy) to redirect to on-
    - premises AD, supports MFA
- Users are managed on the on-premises AD
    - On-prem AD AD Connector
- Simple AD
- AD-compatible managed directory on AWS
- Cannot be joined with on-premises AD
    - Simple AD


## IAM Identity Center – Active Director y Setup

- Connect to an AWS Managed Microsoft AD (Directory Service)
- Integration is out of the box
    - IAM Identity connect AWS Managed
Center Microsoft AD
- Connect to a Self-Managed Directory
- Create Two-way Trust Relationship using AWS Managed Microsoft AD
- Create an AD Connector
    - AWS Managed
Microsoft AD
connect two-way trust relationship
IAM Identity
Center connect
proxy
AD Connector


## AWS Control Tower

- Easy way to set up and govern a secure and compliant multi-account
    - AWS environment based on best practices
- AWS Control Tower uses AWS Organizations to create accounts
- Benefits:
- Automate the set up of your environment in a few clicks
- Automate ongoing policy management using guardrails
- Detect policy violations and remediate them
- Monitor compliance through an interactive dashboard


## AWS Control Tower – Guardrails

- Provides ongoing governance for your Control Tower environment (AWS Accounts)
- Preventive Guardrail – using SCPs (e.g., Restrict Regions across all your accounts)
- Detective Guardrail – using AWS Config (e.g., identify untagged resources)
    - AWS Control Tower
Guardrail trigger notify
(Detective) (NON_COMPLIANT)
AWS Config
Admin
SNS
monitor un-tagged
resources invoke
Member remediate
Accounts (add tags)
Lambda
