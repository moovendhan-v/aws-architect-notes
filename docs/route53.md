# Amazon Route 53


## What is DNS?

- Domain Name System which translates the human friendly hostnames
    - into the machine IP addresses
- www.google.com => 172.217.18.36
- DNS is the backbone of the Internet
- DNS uses hierarchical naming structure
    - com
.
example.com
www.example.com
api.example.com


## DNS Terminologies

- Domain Registrar: Amazon Route 53, GoDaddy, …
- DNS Records: A, AAAA, CNAME, NS, …
- Zone File: contains DNS records
- Name Server: resolves DNS queries (Authoritative or Non-Authoritative)
- Top Level Domain (TLD): .com, .us, .in, .gov, .org, …
- Second Level Domain (SLD): amazon.com, google.com, …
    - URL
http://api.www.example.com.
Root
Protocol TLD
SLD
Sub Domain
FQDN (Fully Qualified Domain Name)


## How DNS Works

Web Server
(example.com)
(IP: 9.10.11.12)
Managed by ICANN
m?
mple.co
exa
Root DNS Server
m
NS
1.2.3.4
.co
example.com?
example.com?
Managed by IANA
9.10.11.12
(Branch of ICANN)
TTL
example.com NS 5.6.7.8
TTL
Web Browser
TLD DNS Server
You want to access Local DNS Server exam
(.com)
example.com exam
ple.com
?
Assigned and Managed by ple.com
your
y
c
o
o
u
m
r
p
IS
a
P
n
d
y
y
o
n
r
a
a
m
ss
ic
ig
a
n
ll
e
y
d by
IP
9.10.11.12
Managed by Domain Registrar
(e.g., Amazon Registrar, Inc.)
SLD DNS Server
(example.com)


## Amazon Route 53

- A highly available, scalable, fully
    - example.com?
managed and Authoritative DNS Amazon
Route 53
- Authoritative = the customer (you)
54.22.33.44
Client
can update the DNS records
- Route 53 is also a Domain Registrar
- Ability to check the health of your
    - resources
AWS Cloud
- The only AWS service which
    - Public IP
provides 100% availability SLA
54.22.33.44
- Why Route 53? 53 is a reference to EC2 Instance
    - the traditional DNS port


## Route 53 – Records

- How you want to route traffic for a domain
- Each record contains:
- Domain/subdomain Name – e.g., example.com
- Record Type – e.g., A or AAAA
- Value – e.g., 12.34.56.78
- Routing Policy – how Route 53 responds to queries
- TTL – amount of time the record cached at DNS Resolvers
- Route 53 supports the following DNS record types:
- (must know) A / AAAA / CNAME / NS
- (advanced) CAA / DS / MX / NAPTR / PTR / SOA / TXT / SPF / SRV


## Route 53 – Record Types

- A – maps a hostname to IPv4
- AAAA – maps a hostname to IPv6
- CNAME – maps a hostname to another hostname
- The target is a domain name which must have an A or AAAA record
- Can’t create a CNAME record for the top node of a DNS namespace (Zone
    - Apex)
- Example: you can’t create for example.com, but you can create for
    - www.example.com
- NS – Name Servers for the Hosted Zone
- Control how traffic is routed for a domain


## Route 53 – Hosted Zones

- A container for records that define how to route traffic to a domain and
    - its subdomains
- Public Hosted Zones – contains records that specify how to route
    - traffic on the Internet (public domain names)
application1.mypublicdomain.com
- Private Hosted Zones – contain records that specify how you route
    - traffic within one or more VPCs (private domain names)
application1.company.internal
- You pay $0.50 per month per hosted zone


## Route 53 – Public vs. Private Hosted Zones

example.com?
VPC
54.22.33.44
Private Hosted Zone
Client
Public Hosted Zone
?
al
n
r
e
t
n
.i 0
e 1
pl
0
.
m 0 . DB Instance
a .
VPC x 0
e 1
. (db.example.internal)
pi
a (Private IP)
S3 Bucket Amazon EC2 Instance Application EC2 Instance EC2 Instance
CloudFront (Public IP) Load Balancer (webapp.example.internal) (api.example.internal)
(Private IP) (Private IP)
?lanretni.elpmaxe.bd
53.0.0.01
Public Hosted Zone Private Hosted Zone


## Route 53 – Records TTL (Time To Live)

- High TTL – e.g., 24 hr
- 
- 
    - L
Po
es
s
s
s ib
tr
ly
a ff
o
ic
u t
o
d
n
a t
R
e
o
d
u
r
t
e
e
c
5
o
3
rds mya
D
p
N
p.
S
e x
R
a
e
m
qu
p
e
le
s
.
t com?
- Low TTL – e.g., 60 sec.
    - Amazon
A 12.34.56.78 Route 53
- More traffic on Route 53 ($$)
    - (with TTL)
TTL
- Records are outdated for less
    - time
Client
HTTP
Request
- Easy to change records
    - W
T
i
h
ll
e
c a
T
c
T
h
L
e
o
t
f
h
t
e
h e
re
r
s
e
u
c
l
o
t
r
f
d
or HTTP
Response
- Except for Alias records, TTL
    - is mandatory for each DNS
record
Web Server


## CNAME vs Alias

- AWS Resources (Load Balancer, CloudFront...) expose an AWS hostname:
- lb1-1234.us-east-2.elb.amazonaws.com and you want myapp.mydomain.com
- CNAME:
- Points a hostname to any other hostname. (app.mydomain.com => blabla.anything.com)
- ONLY FOR NON ROOT DOMAIN (aka. something.mydomain.com)
- Alias:
- Points a hostname to an AWS Resource (app.mydomain.com => blabla.amazonaws.com)
- Works for ROOT DOMAIN and NON ROOT DOMAIN (aka mydomain.com)
- Free of charge
- Native health check


## Route 53 – Alias Records

Amazon
Route 53
- Maps a hostname to an AWS resource
- An extension to DNS functionality
- Automatically recognizes changes in the Alias Record (Enabled)
    - Record Name Type Value
resource’s IP addresses
example.com A MyALB-123456789.us-
east-
- Unlike CNAME, it can be used for the top node
1.elb.amazonaws.com
of a DNS namespace (Zone Apex), e.g.:
example.com
- Alias Record is always of type A/AAAA for
    - MyALB-123456789.us-east-1.elb.amazonaws.com
AWS resources (IPv4 / IPv6)
AWS-Managed
(IP Addresses might change)
- You can’t set the TTL
    - Application
Load Balancer


## Route 53 – Alias Records Targets

- Elastic Load Balancers
- CloudFront Distributions
    - Elastic Amazon Amazon
- API Gateway
    - Load Balancer CloudFront API Gateway
- Elastic Beanstalk environments
- S3 Websites
- VPC Interface Endpoints Elastic Beanstalk S3 Websites VPC Interface
    - Endpoints
- Global Accelerator accelerator
- Route 53 record in the same hosted zone
    - Global Accelerator Route 53 Record
- You cannot set an ALIAS record for an EC2 DNS name (same Hosted Zone)


## Route 53 – Routing Policies

- Define how Route 53 responds to DNS queries
- Don’t get confused by the word “Routing”
- It’s not the same as Load balancer routing which routes the traffic
- DNS does not route any traffic, it only responds to the DNS queries
- Route 53 Supports the following Routing Policies
- Simple
- Weighted
- Failover
- Latency based
- Geolocation
- Multi-Value Answer
- Geoproximity (using Route 53 Traffic Flow feature)


## Routing Policies – Simple

Single Value
- Typically, route traffic to a single
    - foo.example.com
resource
A 11.22.33.44
- Can specify multiple values in the
    - Client
same record Amazon
Route 53
- If multiple values are returned, a
    - random one is chosen by the client
Multiple Value
- When Alias enabled, specify only
    - one AWS resource
foo.example.com
- Can’t be associated with Health
    - Checks
A 11.22.33.44
Client
A 55.66.77.88
Amazon
A 99.11.22.33
chooses
Route 53
a random value


## Routing Policies – Weighted

- Control the % of the requests that go to each
    - specific resource
- Assign each record a relative weight:
    - % Weight: 70
0
7
!"#$%& ()* + ,-".#(#. *".)*/
- 𝑡𝑟𝑎𝑓𝑓𝑖𝑐 (%) =
    - 012 )( +33 &%" 4"#$%&, ()* +33 *".)*/,
- Weights don’t need to sum up to 100
- DNS records must have the same name and type
- Can be associated with Health Checks 20%
- Use cases: load balancing between regions, testing
    - new application versions…
Amazon Weight: 20
Route 53
- Assign a weight of 0 to a record to stop sending
    - 1
0
traffic to a resource %
- If all records have weight of 0, then all records will
    - be returned equally
Weight: 10


## Routing Policies – Latency-based

- Redirect to the resource that
    - has the least latency close to us
- Super helpful when latency for
    - users is a priority
- Latency is based on traffic
    - between users and AWS
Regions
ALB
- Germany users may be
    - (us-east-1)
directed to the US (if that’s the
lowest latency)
ALB
(ap-southeast-1)
- Can be associated with Health
    - Checks (has a failover
capability)


## Route 53 – Health Checks

Amazon Route 53
DNS Record
- HTTP Health Checks are only for public
    - (latency, geoproximity, …)
resources
- Health Check => Automated DNS Failover:
    - Health Check Health Check
1. Health checks that monitor an endpoint
(application, server, other AWS resource)
2. Health checks that monitor other health
us-east-1 eu-west-1
checks (Calculated Health Checks)
3. Health checks that monitor CloudWatch
Alarms (full control !!) – e.g., throttles of
ALB ALB
DynamoDB, alarms on RDS, custom metrics,
… (helpful for private resources)
Auto Scaling group Auto Scaling group
- Health Checks are integrated with CW
    - metrics
EC2 Instance EC2 Instance


## Health Checks – Monitor an Endpoint

- About 15 global health checkers will check the Health Checker Health Checker Health Checker
    - endpoint health (us-east-1) (us-west-1) (sa-east-1)
- Healthy/Unhealthy Threshold – 3 (default) H
    - t T
o T
- Interval – 30 sec (can set to 10 sec – higher cost) P
    - / 2 h r
0 e e
- Supported protocol: HTTP, HTTPS and TCP 0 a q
    - c
l
t
u
o h e
- If > 18% of health checkers report the endpoint is d s t
    - e
healthy, Route 53 considers it Healthy. Otherwise, it’s
Unhealthy
eu-west-1
- Ability to choose which locations you want Route 53 to Must allow incoming
    - use requests from Route 53
Health Checkers IP
- Health Checks pass only when the endpoint
    - address range
ALB
responds with the 2xx and 3xx status codes
- Health Checks can be setup to pass / fail based on
    - the text in the first 5120 bytes of the response
Auto Scaling group
- Configure you router/firewall to allow incoming
    - requests from Route 53 Health Checkers
EC2 Instance
https://ip-ranges.amazonaws.com/ip-ranges.json


## Route 53 – Calculated Health Checks

Amazon Route 53
- Combine the results of multiple Health
    - Checks into a single Health Check
Health Check
- You can use OR, AND, or NOT
    - (Parent)
- Can monitor up to 256 Child Health Checks
- Specify how many of the health checks need
    - to pass to make the parent pass
Health Check Health Check Health Check
- Usage: perform maintenance to your website
    - (Child) (Child) (Child)
without causing all health checks to fail
monitor monitor monitor
EC2 Instance EC2 Instance EC2 Instance


## Health Checks – Private Hosted Zones

- Route 53 health checkers are outside the
    - VPC
VPC
- They can’t access private endpoints
    - Private subnet
(private VPC or on-premises resource)
Health Checker
(us-east-1)
- You can create a CloudWatch Metric and
    - monitor
associate a CloudWatch Alarm, then
create a Health Check that checks the
monitor
alarm itself
CloudWatch
Alarm


## Routing Policies – Failover (Active-Passive)

EC2 Instance
(Primary)
Health Check
(mandatory)
DNS Requests
Failover
Client
Amazon
Route 53
EC2 Instance
(Secondary – Disaster Recovery)


## Routing Policies – Geolocation

A 11.22.33.44
- Different from Latency-based!
- This routing is based on user location
- Specify location by Continent, Country
    - or by US State (if there’s overlapping,
most precise location selected)
Default
A 99.11.22.33
- Should create a “Default” record (in
    - case there’s no match on location)
- Use cases: website localization, restrict
    - content distribution, load balancing, …
- Can be associated with Health Checks
    - A 55.66.77.88


## Routing Policies – Geoproximity

- Route traffic to your resources based on the geographic location of users and
    - resources
- Ability to shift more traffic to resources based on the defined bias
- To change the size of the geographic region, specify bias values:
- To expand (1 to 99) – more traffic to the resource
- To shrink (-1 to -99) – less traffic to the resource
- Resources can be:
- AWS resources (specify AWS region)
- Non-AWS resources (specify Latitude and Longitude)
- You must use Route 53 Traffic Flow to use this feature


## Routing Policies – Geoproximity

us-west-1 us-east-1
Bias: 0 Bias: 0


## Routing Policies – Geoproximity

us-west-1 us-east-1
Bias: 0 Bias: 50
Higher bias in us-east-1


## Routing Policies – IP-based Routing

User B User A
- Routing is based on clients’ IP addresses (200.5.4.100) (203.0.113.56)
- You provide a list of CIDRs for your clients
    - and the corresponding endpoints/locations
Route 53
(user-IP-to-endpoint mappings)
CIDR Collection
- Use cases: Optimize performance, reduce
    - Locations CIDR blocks
network costs… location-1 203.0.113.0/24
location-2 200.5.4.0/24
- Example: route end users from a particular
    - Records
ISP to a specific endpoint Record Name Value IP-based
example.com 1.2.3.4 location-1
example.com 5.6.7.8 location-2
EC2 Instance EC2 Instance
(5.6.7.8) (1.2.3.4)


## Routing Policies – Multi-Value

- Use when routing traffic to multiple resources
- Route 53 return multiple values/resources
- Can be associated with Health Checks (return only values for healthy resources)
- Up to 8 healthy records are returned for each Multi-Value query
- Multi-Value is not a substitute for having an ELB


## Domain Registar vs. DNS Service

- You buy or register your domain name with a Domain Registrar typically by

paying annual charges (e.g., GoDaddy, Amazon Registrar Inc., …)
- The Domain Registrar usually provides you with a DNS service to manage
    - your DNS records
- But you can use another DNS service to manage your DNS records
- Example: purchase the domain from GoDaddy and use Route 53 to manage
    - your DNS records
purchase
example.com manage DNS records
User
Amazon
Route 53


## GoDaddy as Registrar & Route 53 as DNS Service

Amazon Public Hosted Zone
Route 53 stephanetheteacher.com


## 3rd Par ty Registrar with Amazon Route 53

- If you buy your domain on a 3rd party registrar, you can still use Route
    - 53 as the DNS Service provider
1. Create a Hosted Zone in Route 53
2. Update NS Records on 3rd party website to use Route 53 Name
Servers
- Domain Registrar != DNS Service
- But every Domain Registrar usually comes with some DNS features


## Route 53 – Hybrid DNS

- By default, Route 53 Resolver
    - automatically answers DNS queries for:
Public Name Server
- Local domain names for EC2 instances
- Records in Private Hosted Zones
- Records in public Name Servers
    - Region
- Hybrid DNS – resolving DNS queries
    - VPC
between VPC (Route 53 Resolver) and
Private Hosted Zone
your networks (other DNS Resolvers)
- Networks can be:
    - Route 53
- VPC itself / Peered VPC
    - Resolver EC2 Instance
- On-premises Network (connected through (ec2-192-0-2-44.compute-1.amazonaws.com)
    - Direct Connect or AWS VPN)


## Route 53 – Resolver Endpoints

- Inbound Endpoint – allows your DNS Resolvers to resolve domain names for

AWS resources (e.g., EC2 instances) and records in Private Hosted Zones
us-east-1 On-Premises Data Center
Private Hosted Zone
(aws.private)
VPC
lookup
DNS
Query
app.aws.private?
Private Subnet
DNS Resolvers
Resolver (onpremise.private) Route 53
EC2 Instance Inbound Endpoint
Resolver
DNS Query
(app.aws.private)
app.aws.private?
VPN or DX connection
Server
(web.onpremise.private)


## Route 53 – Resolver Endpoints

- Outbound Endpoint
- Route 53 Resolver forwards DNS queries to your DNS Resolvers
    - us-east-1 On-Premises Data Center
Private Hosted Zone
(aws.private)
VPC
web.o D np N r S e Q m u is e e r . y p
P
r
r
iv
iv
a
a
t
t
e
e
?
Subnet
(a E p C p 2 .a I w n s s .p ta ri n va c t e e) web.on D p N r S e m
Qu
is
e
e
r
.
y
private? DNS Resolvers
(onpremise.private)
Resolver
Route 53
Outbound Endpoint
Resolver
VPN or DX connection
Server
(web.onpremise.private)
