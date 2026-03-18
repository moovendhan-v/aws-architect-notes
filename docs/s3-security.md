# Amazon S3 – Security


## Amazon S3 – Object Encryption

- You can encrypt objects in S3 buckets using one of 4 methods
- Server-Side Encryption (SSE)
- Server-Side Encryption with Amazon S3-Managed Keys (SSE-S3) – Enabled by Default
- Encrypts S3 objects using keys handled, managed, and owned by AWS
- Server-Side Encryption with KMS Keys stored in AWS KMS (SSE-KMS)
- Leverage AWS Key Management Service (AWS KMS) to manage encryption keys
- Server-Side Encryption with Customer-Provided Keys (SSE-C)
- When you want to manage your own encryption keys
- Client-Side Encryption
- It’s important to understand which ones are for which situation for the exam


## Amazon S3 Encryption – SSE-S3

- Encryption using keys handled, managed, and owned by AWS
- Object is encrypted server-side
- Encryption type is AES-256
- Must set header "x-amz-server-side-encryption": "AES256"
- Enabled by default for new buckets & new objects
    - Amazon S3
Object
upload
+
Encryption
HTTP(S) + Header
User
S3 Bucket
S3 Owned Key


## Amazon S3 Encryption – SSE-KMS

- Encryption using keys handled and managed by AWS KMS (Key Management Service)
- KMS advantages: user control + audit key usage using CloudTrail
- Object is encrypted server side
- Must set header "x-amz-server-side-encryption": "aws:kms"
    - Amazon S3
Object
upload
+
Encryption
HTTP(S) + Header
User
S3 Bucket
KMS Key
AWS KMS


## SSE-KMS Limitation

- If you use SSE-KMS, you may be impacted
    - S3 Bucket KMS Key
by the KMS limits
API call
- When you upload, it calls the
    - GenerateDataKey KMS API
Upload / download
- When you download, it calls the Decrypt
    - SSE-KMS
KMS API
- Count towards the KMS quota per second
    - (5500, 10000, 30000 req/s based on region)
Users
- You can request a quota increase using the
    - Service Quotas Console


## Amazon S3 Encryption – SSE-C

- Server-Side Encryption using keys fully managed by the customer outside of AWS
- Amazon S3 does NOT store the encryption key you provide
- HTTPS must be used
- Encryption key must provided in HTTP headers, for every HTTP request made
    - Amazon S3
Object
+
upload
+
Encryption
HTTPS ONLY
User
+ Key in Header
S3 Bucket
Client-Provided Key


## Amazon S3 Encryption – Client-Side Encryption

- Use client libraries such as Amazon S3 Client-Side Encryption Library
- Clients must encrypt data themselves before sending to Amazon S3
- Clients must decrypt data themselves when retrieving from Amazon S3
- Customer fully manages the keys and encryption cycle
    - Amazon S3
File
+ upload
Encryption
HTTP(S)
File
S3 Bucket
(encrypted)
Client Key


## Amazon S3 – Encryption in transit (SSL/TLS)

- Encryption in flight is also called SSL/TLS
- Amazon S3 exposes two endpoints:
- HTTP Endpoint – non encrypted
- HTTPS Endpoint – encryption in flight
- HTTPS is recommended
- HTTPS is mandatory for SSE-C
- Most clients would use the HTTPS endpoint by default


## Amazon S3 – Force Encryption in Transit

aws:SecureTranspor t
Account B
User
http
S3 Bucket
(my-bucket)
https
Bucket Policy
User


## Amazon S3 – Default Encryption vs. Bucket Policies

- SSE-S3 encryption is automatically applied to new objects stored in S3 bucket
- Optionally, you can “force encryption” using a bucket policy and refuse any API call

to PUT an S3 object without encryption headers (SSE-KMS or SSE-C)
- Note: Bucket Policies are evaluated before “Default Encryption”


## What is CORS?

- Cross-Origin Resource Sharing (CORS)
- Origin = scheme (protocol) + host (domain) + port
- example: https://www.example.com (implied port is 443 for HTTPS, 80 for HTTP)
- Web Browser based mechanism to allow requests to other origins while
    - visiting the main origin
- Same origin: http://example.com/app1 & http://example.com/app2
- Different origins: http://www.example.com & http://other.example.com
- The requests won’t be fulfilled unless the other origin allows for the

requests, using CORS Headers (example: Access-Control-Allow-Origin)


## What is CORS?

OPTIONS /
Host: www.other.com
Origin: https://www.example.com
Preflight Request
Access-Control-Allow-Origin: https://www.example.com
Access-Control-Allow-Methods: GET, PUT, DELETE
HTTPS Request
Preflight Response
Web Browser
Web Server Web Server
(Origin) GET / (Cross-Origin)
https://www.example.com Host: www.other.com https://www.other.com
Origin: https://www.example.com
CORS Headers received already by the Origin
The Web Browser can make requests


## Amazon S3 – CORS

- If a client makes a cross-origin request on our S3 bucket, we need to enable
    - the correct CORS headers
- It’s a popular exam question
- You can allow for a specific origin or for * (all origins)
    - GET /index.html
Host: http://my-bucket-html.s3-website.us-west-2.amazonaws.com S3 Bucket
(my-bucket-html)
(Static Website Enabled)
index.html
GET /images/coffee.jpg
Web Browser Host: http://my-bucket-assets.s3-website.us-west-2.amazonaws.com
Origin: http://my-bucket-html.s3-website.us-west-2.amazonaws.com S3 Bucket
(my-bucket-assets)
(Static Website Enabled)
Access-Control-Allow-Origin: http://my-bucket-html.s3-website.us-west-2.amazonaws.com


## Amazon S3 – MFA Delete

- MFA (Multi-Factor Authentication) – force users to generate a code on a

device (usually a mobile phone or hardware) before doing important
operations on S3
- MFA will be required to:
- Permanently delete an object version
    - Google Authenticator
- Suspend Versioning on the bucket
- MFA won’t be required to:
- Enable Versioning
- List deleted versions MFA Hardware Device
- To use MFA Delete, Versioning must be enabled on the bucket
- Only the bucket owner (root account) can enable/disable MFA Delete


## S3 Access Logs

- For audit purpose, you may want to log all access to S3 buckets
    - requests
- Any request made to S3, from any account, authorized or denied,
    - will be logged into another S3 bucket
- That data can be analyzed using data analysis tools…
- The target logging bucket must be in the same AWS region
    - My-bucket
Log all
requests
- The log format is at:

https://docs.aws.amazon.com/AmazonS3/latest/dev/LogFormat.html
Logging Bucket


## S3 Access Logs: Warning

- Do not set your logging bucket to be the monitored bucket
- It will create a logging loop, and your bucket will grow exponentially
    - Logging loop
PutObject
App Bucket &
Logging Bucket
Do not try this at home J


## Amazon S3 – Pre-Signed URLs

Owner
- Generate pre-signed URLs using the S3 Console, AWS CLI or SDK
- URL Expiration
- S3 Console – 1 min up to 720 mins (12 hours)
- AWS CLI – configure expiration with --expires-in parameter in seconds
    - (default 3600 secs, max. 604800 secs ~ 168 hours)
- Users given a pre-signed URL inherit the permissions of the user
    - that generated the URL for GET / PUT
S3 Bucket
- Examples:
    - (Private)
- Allow only logged-in users to download a premium video from your S3
    - bucket
- Allow an ever-changing list of users to download files by generating URLs
    - dynamically
- Allow temporarily a user to upload a file to a precise location in your S3
    - bucket
User
pre-signed
URL
generate
URL
URL
URL


## S3 Glacier Vault Lock

- Adopt a WORM (Write Once Read
    - Object
Many) model
- Create a Vault Lock Policy
- Lock the policy for future edits
    - (can no longer be changed or deleted)
- Helpful for compliance and data
    - Vault Lock Policy
retention Object can’t be deleted


## S3 Object Lock (versioning must be enabled)

- Adopt a WORM (Write Once Read Many) model
- Block an object version deletion for a specified amount of time
- Retention mode - Compliance:
- Object versions can't be overwritten or deleted by any user, including the root user
- Objects retention modes can't be changed, and retention periods can't be shortened
- Retention mode - Governance:
- Most users can't overwrite or delete an object version or alter its lock settings
- Some users have special permissions to change the retention or delete the object
- Retention Period: protect the object for a fixed period, it can be extended
- Legal Hold:
- protect the object indefinitely, independent from retention period
- can be freely placed and removed using the s3:PutObjectLegalHold IAM permission


## S3 – Access Points

Policy
Grant R/W to
Users /finance prefix Finance
S3 Bucket
Access Point
(Finance) Simple Bucket
Policy
Policy
Grant R/W to
/finance/…
Users /sales prefix Sales
(Sales) Access Point
Policy /sales/…
Grant R to
Users entire bucket Analytics
(Analytics) Access Point
- Access Points simplify security management for S3 Buckets
- Each Access Point has:
- its own DNS name (Internet Origin or VPC Origin)
- an access point policy (similar to bucket policy) – manage security at scale


## S3 – Access Points – VPC Origin

- We can define the access
    - VPC
Access Point
point to be accessible S3 Bucket
EC2 Instance VPC Endpoint VPC Origin
only from within the VPC
- You must create a VPC
    - Endpoint to access the Endpoint Access Point Bucket
Policy Policy Policy
Access Point (Gateway
or Interface Endpoint)
- The VPC Endpoint Policy
    - must allow access to the
target bucket and Access
Point


## S3 Object Lambda

AWS Cloud
- Use AWS Lambda Functions to
    - change the object before it is
retrieved by the caller application Original
S3 Bucket
Object
- Only one S3 bucket is needed, on
    - E-Commerce
top of which we create S3 Access
Application
Point and S3 Object Lambda Access Supporting
S3 Access Point
Points. S3 Object Lambda Redacting
Access Point Lambda Function
- Use Cases:
    - Redacted
- Redacting personally identifiable Object
    - information for analytics or non-
Analytics
production environments.
Application
S3 Object Lambda Enriching
- Converting across data formats, such Access Point Lambda Function

as converting XML to JSON.
Enriched
- Resizing and watermarking images on Object
    - the fly using caller-specific details, such
Marketing
as the user who requested the object.
Application Customer Loyalty
Database
