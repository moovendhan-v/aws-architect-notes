# CloudFront & Global Accelerator


## Amazon CloudFront

- Content Delivery Network (CDN)
- Improves read performance, content
    - is cached at the edge
- Improves users experience
- Hundreds of Points of Presence
    - globally (edge locations, caches)
- DDoS protection (because
    - worldwide), integration with Shield,
Source: https://aws.amazon.com/cloudfront/features/?nc=sn&loc=2
AWS Web Application Firewall


## CloudFront – Origins

- S3 bucket
- For distributing files and caching them at the edge
- For uploading files to S3 through CloudFront
- Secured using Origin Access Control (OAC)
- VPC Origin
- For applications hosted in VPC private subnets
- Private Application Load Balancer / Network Load Balancer / EC2 Instances
- Custom Origin (HTTP)
- S3 website (must first enable the bucket as a static S3 website)
- Any public HTTP backend you want (example: Public ALB)


## CloudFront at a high level

GET /beach.jpg?size=300x300 HTTP/1.1
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
Host: www.example.com
Accept-Encoding: gzip, deflate
Origin
Forward Request
to your Origin
S3
CloudFront Edge Location or
Client
HTTP
Local Cache


## CloudFront – S3 as an Origin

AWS Cloud
Public www
Private AWS
Edge Private AWS Edge
Los Angeles Mumbai
Private AWS Private AWS
Origin (S3 bucket)
OAC
Public www
Edge Edge
São Paulo Melbourne
Origin Access Control
+ S3 bucket policy


## CloudFront vs S3 Cross Region Replication

- CloudFront:
- Global Edge network
- Files are cached for a TTL (maybe a day)
- Great for static content that must be available everywhere
- S3 Cross Region Replication:
- Must be setup for each region you want replication to happen
- Files are updated in near real-time
- Read only
- Great for dynamic content that needs to be available at low-latency in few
    - regions


## CloudFront – ALB or EC2 as an origin

Using VPC Origins
- Allows you to deliver content from your applications hosted in your

VPC private subnets (no need to expose them on the Internet)
- Deliver traffic to private:
- Application Load Balancer
- Network Load Balancer
- EC2 Instances VPC
    - Private Subnet
Application Load Balancer
VPC
Users
CloudFront
Origin Network Load Balancer
EC2 Instance
Edge Location


## CloudFront – ALB or EC2 as an origin

Using Public Network
Security group
Allow Public IP of Edge Locations
http://d7uri8nf7uskq.cloudfront.net/tools/list-cloudfront-ips
Edge Location EC2 Instances
Must be Public
Security group Security group
Allow Public IP of Allow Security Group
Edge Locations of Load Balancer
Edge Location EC2 Instances
Application Load Balancer
Public IPs Can be Private
Must be Public


## CloudFront Geo Restriction

- You can restrict who can access your distribution
- Allowlist: Allow your users to access your content only if they're in one of the

countries on a list of approved countries.
- Blocklist: Prevent your users from accessing your content if they're in one of the

countries on a list of banned countries.
- The “country” is determined using a 3rd party Geo-IP database
- Use case: Copyright Laws to control access to content


## CloudFront – Cache Invalidations

- In case you update the back-end
    - Invalidate
- /index.html
    - origin, CloudFront doesn’t know GET /index.html
- /images/*
    - about it and will only get the
refreshed content after the TTL has
CloudFront
expired
- However, you can force an entire or
    - invalidate
partial cache refresh (thus bypassing
the TTL) by performing a CloudFront
Edge Location Edge Location
Invalidation
Cache Cache
- You can invalidate all files (*) or a
    - special path (/images/*)
index.html /images/ index.html /images/
update files
S3 Bucket
(origin)


## Global users for our application

- You have deployed an
    - application and have global
users who want to access it
directly. hops
Europe
America
- They go over the public
    - internet, which can add a lot of
latency due to many hops
Public ALB
- We wish to go as fast as
    - possible through AWS network
Australia
India
to minimize latency


## Unicast IP vs Anycast IP

Client
- Unicast IP: one server holds one IP
    - address
12.34.56.78 98.76.54.32
- Anycast IP: all servers hold the same
    - Client
IP address and the client is routed to
the nearest one
12.34.56.78 12.34.56.78


## AWS Global Accelerator

- Leverage the AWS internal
    - network to route to your
application
- 2 Anycast IP are created for your Edge location Europe
    - America
application
- The Anycast IP send traffic directly
    - to Edge Locations
Private AWS
Public ALB
- The Edge locations send the traffic
    - Australia
to your application India


## AWS Global Accelerator

- Works with Elastic IP, EC2 instances, ALB, NLB, public or private
- Consistent Performance
- Intelligent routing to lowest latency and fast regional failover
- No issue with client cache (because the IP doesn’t change)
- Internal AWS network
- Health Checks
- Global Accelerator performs a health check of your applications
- Helps make your application global (failover less than 1 minute for unhealthy)
- Great for disaster recovery (thanks to the health checks)
- Security
- only 2 external IP need to be whitelisted
- DDoS protection thanks to AWS Shield


## AWS Global Accelerator vs CloudFront

- They both use the AWS global network and its edge locations around the world
- Both services integrate with AWS Shield for DDoS protection.
- CloudFront
- Improves performance for both cacheable content (such as images and videos)
- Dynamic content (such as API acceleration and dynamic site delivery)
- Content is served at the edge
- Global Accelerator
- Improves performance for a wide range of applications over TCP or UDP
- Proxying packets at the edge to applications running in one or more AWS Regions.
- Good fit for non-HTTP use cases, such as gaming (UDP), IoT (MQTT), or Voice over IP
- Good for HTTP use cases that require static IP addresses
- Good for HTTP use cases that required deterministic, fast regional failover
