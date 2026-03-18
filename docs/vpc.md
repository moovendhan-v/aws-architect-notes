# Amazon VPC


## VPC Components Diagram

Amazon
DynamoDB
VPC Flow Logs
Region
NACL NACL
VPC
Internet
Corporate
www Public Subnet Private Subnet Data Center
CloudWatch
Security Group
Internet Router
Gateway
NAT Gateway
Transit Private EC2 Instance S3
Gateway
Route Route Server
VPN Customer
Table Security Group Table Security Group VPC Gateway
Endpoint
S2S VPN
Connection
DX
VPC Peering Public EC2 Instance Private EC2 Instance
Connections VPN
Gateway
Direct Connect
Availability Zone
Connection
DX Location


## Understanding CIDR – IPv4

- Classless Inter-Domain Routing – a method for allocating IP addresses
- Used in Security Groups rules and AWS networking in general
- They help to define an IP address range:
- We’ve seen WW.XX.YY.ZZ/32 => one IP
- We’ve seen 0.0.0.0/0 => all IPs
- But we can define:192.168.0.0/26 =>192.168.0.0 – 192.168.0.63 (64 IP addresses)


## Understanding CIDR – IPv4

- A CIDR consists of two components
- Base IP
- Represents an IP contained in the range (XX.XX.XX.XX)
- Example: 10.0.0.0, 192.168.0.0, …
- Subnet Mask
- Defines how many bits can change in the IP
- Example: /0, /24, /32
- Can take two forms:
- /8 ó 255.0.0.0
- /16 ó 255.255.0.0
- /24 ó 255.255.255.0
- /32 ó 255.255.255.255


## Understanding CIDR – Subnet Mask

- The Subnet Mask basically allows part of the underlying IP to get
    - additional next values from the base IP
192 . 168 . 0 . 0 /32 => allows for 1 IP (2!) 192.168.0.0
192 . 168 . 0 . 0 /31 => allows for 2 IP (2") 192.168.0.0 -> 192.168.0.1 Quick Memo
192 . 168 . 0 . 0 /30 => allows for 4 IP (2#) 192.168.0.0 -> 192.168.0.3
192 . 168 . 0 . 0 /29 => allows for 8 IP (2$) 192.168.0.0 -> 192.168.0.7 Octets
. . .
192 . 168 . 0 . 0 /28 => allows for 16 IP (2%) 192.168.0.0 -> 192.168.0.15 1*+ 2,- 3.- 4+/
192 . 168 . 0 . 0 /27 => allows for 32 IP (2&) 192.168.0.0 -> 192.168.0.31
192 . 168 . 0 . 0 /26 => allows for 64 IP (2') 192.168.0.0 -> 192.168.0.63 • /32 – no octet can change
192 . 168 . 0 . 0 /25 => allows for 128 IP (2() 192.168.0.0 -> 192.168.0.127 • /24 – last octet can change
192 . 168 . 0 . 0 /24 => allows for 256 IP (2)) 192.168.0.0 -> 192.168.0.255 • /16 – last 2 octets can change
… • /8 – last 3 octets can change
- /0 – all octets can change

192 . 168 . 0 . 0 /16 => allows for 65,536 IP (2"') 192.168.0.0 -> 192.168.255.255
…
0 . 0 . 0 . 0 /0 => allows for All IPs 0.0.0.0 -> 255.255.255.255


## Understanding CIDR – Little Exercise

- 192.168.0.0/24 = … ?
- 192.168.0.0 – 192.168.0.255 (256 IPs)
- 192.168.0.0/16 = … ?
- 192.168.0.0 – 192.168.255.255 (65,536 IPs)
- 134.56.78.123/32 = … ?
- Just 134.56.78.123
- 0.0.0.0/0
- All IPs!
- When in doubt, use this website https://www.ipaddressguide.com/cidr


## Public vs. Private IP (IPv4)

- The Internet Assigned Numbers Authority (IANA) established certain

blocks of IPv4 addresses for the use of private (LAN) and public
(Internet) addresses
- Private IP can only allow certain values:
- 10.0.0.0 – 10.255.255.255 (10.0.0.0/8) ç in big networks
- 172.16.0.0 – 172.31.255.255 (172.16.0.0/12) ç AWS default VPC in that range
- 192.168.0.0 – 192.168.255.255 (192.168.0.0/16) ç e.g., home networks
- All the rest of the IP addresses on the Internet are Public


## Default VPC Walkthrough

- All new AWS accounts have a default VPC
- New EC2 instances are launched into the default VPC if no subnet is
    - specified
- Default VPC has Internet connectivity and all EC2 instances inside it
    - have public IPv4 addresses
- We also get a public and a private IPv4 DNS names


## VPC in AWS – IPv4

- VPC = Virtual Private Cloud
- You can have multiple VPCs in an AWS region (max. 5 per region – soft limit)
- Max. CIDR per VPC is 5, for each CIDR:
- Min. size is /28 (16 IP addresses)
- Max. size is /16 (65536 IP addresses)
- Because VPC is private, only the Private IPv4 ranges are allowed:
- 10.0.0.0 – 10.255.255.255 (10.0.0.0/8)
- 172.16.0.0 – 172.31.255.255 (172.16.0.0/12)
- 192.168.0.0 – 192.168.255.255 (192.168.0.0/16)
- Your VPC CIDR should NOT overlap with your other networks (e.g., corporate)


## State of Hands-on

Region
VPC


## Adding Subnets

Region
VPC
Public Subnet Private Subnet
Availability Zone


## VPC – Subnet (IPv4)

- AWS reserves 5 IP addresses (first 4 & last 1) in each subnet
- These 5 IP addresses are not available for use and can’t be assigned to an
    - EC2 instance
- Example: if CIDR block 10.0.0.0/24, then reserved IP addresses are:
- 10.0.0.0 – Network Address
- 10.0.0.1 – reserved by AWS for the VPC router
- 10.0.0.2 – reserved by AWS for mapping to Amazon-provided DNS
- 10.0.0.3 – reserved by AWS for future use
- 10.0.0.255 – Network Broadcast Address. AWS does not support broadcast in a VPC,
    - therefore the address is reserved
- Exam Tip, if you need 29 IP addresses for EC2 instances:
- You can’t choose a subnet of size /27 (32 IP addresses, 32 – 5 = 27 < 29)
- You need to choose a subnet of size /26 (64 IP addresses, 64 – 5 = 59 > 29)


## Internet Gateway (IGW)

- Allows resources (e.g., EC2 instances) in a VPC connect to the Internet
- It scales horizontally and is highly available and redundant
- Must be created separately from a VPC
- One VPC can only be attached to one IGW and vice versa
- Internet Gateways on their own do not allow Internet access…
- Route tables must also be edited!


## Adding Internet Gateway

Region
VPC
Public Subnet Private Subnet
Internet
Gateway
Availability Zone


## Editing Route Tables

Region
VPC
Internet
www
Public Subnet Private Subnet
Internet Router
Gateway
Route
Table Security Group
Public EC2 Instance
Availability Zone


## Bastion Hosts

Users
- We can use a Bastion Host to SSH into
    - our private EC2 instances
SSH
- The bastion is in the public subnet which is
    - VPC
then connected to all other private subnets
Public Subnet
- Bastion Host security group must allow
    - Security Group (BastionHost-SG)
inbound from the internet on port 22 from
EC2 Instance
restricted CIDR, for example the public
(Bastion Host)
CIDR of your corporation
SSH
Private Subnet
- Security Group of the EC2 Instances must

allow the Security Group of the Bastion Security Group (LinuxInstance-SG)
Host, or the private IP of the Bastion host


## NAT Instance (outdated, but still at the exam)

Server
- NAT = Network Address Translation
    - (IP: 50.60.4.10)
- Allows EC2 instances in private subnets to
    - Src.: 50.60.4.10
connect to the Internet
Dest.: 12.34.56.78
- Must be launched in a public subnet VPC Dest.: 50.60.4.10
    - Src.: 12.34.56.78
- Must disable EC2 setting: Source /
    - Public Subnet
destination Check
Security Group (NATInstance-SG)
EIP
- Must have Elastic IP attached to it
    - (IP: 12.34.56.78)
NAT Instance
- Route Tables must be configured to route Dest.: 50.60.4.10
    - Src.: 50.60.4.10
Src.: 10.0.0.20
traffic from private subnets to the NAT
Dest.: 10.0.0.20
Instance Private Subnet
IP: 10.0.0.10 IP: 10.0.0.20


## NAT Instance

Region
VPC
Internet
www
Public Subnet Private Subnet
Security Group Security Group
Internet Router
Gateway
EIP
NAT Instance Private EC2 Instance
Route Route
Table Security Group Table
Public EC2 Instance
Availability Zone


## NAT Instance – Comments

- Pre-configured Amazon Linux AMI is available
- Reached the end of standard support on December 31, 2020
- Not highly available / resilient setup out of the box
- You need to create an ASG in multi-AZ + resilient user-data script
- Internet traffic bandwidth depends on EC2 instance type
- You must manage Security Groups & rules:
- Inbound:
- Allow HTTP / HTTPS traffic coming from Private Subnets
- Allow SSH from your home network (access is provided through Internet Gateway)
- Outbound:
- Allow HTTP / HTTPS traffic to the Internet


## NAT Gateway

- AWS-managed NAT, higher bandwidth, high availability, no administration
- Pay per hour for usage and bandwidth
- NATGW is created in a specific Availability Zone, uses an Elastic IP
- Can’t be used by EC2 instance in the same subnet (only from other
    - subnets)
- Requires an IGW (Private Subnet => NATGW => IGW)
- 5 Gbps of bandwidth with automatic scaling up to 100 Gbps
- No Security Groups to manage / required


## NAT Gateway

Region
VPC
Internet
www
Public Subnet Private Subnet
Security Group
Internet Router
Gateway
NAT Gateway
Private EC2 Instance
Route Route
Table Security Group Table
Public EC2 Instance
Availability Zone


## NAT Gateway with High Availability

Internet
- NAT Gateway is resilient within a
    - www
single Availability Zone
Region
Internet
Gateway
VPC
- Must create multiple NAT
    - Gateways in multiple AZs for Router
Public Subnet Public Subnet
fault-tolerance
NAT Gateway NAT Gateway
- There is no cross-AZ failover Private Subnet Private Subnet
    - needed because if an AZ goes
down it doesn't need NAT
EC2 Instance EC2 Instance
AZ - A AZ - B


## NAT Gateway vs. NAT Instance

NAT Gateway NAT Instance
Availability Highly available within AZ (create in another AZ) Use a script to manage failover between instances
Bandwidth Up to 100 Gbps Depends on EC2 instance type
Maintenance Managed by AWS Managed by you (e.g., software, OS patches, …)
Cost Per hour & amount of data transferred Per hour, EC2 instance type and size, + network $
Public IPv4
Private IPv4
Security Groups
Use as Bastion Host?
More at: https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-comparison.html


## Security Groups & NACLs

Subnet
Security Group
EC2 Instance
LCAN
Incoming Request
Subnet
NACL Inbound SG Inbound Security Group
Rules Rules
NACL Outbound
Outbound Allowed EC2 Instance Rules (Stateless)
(Stateful)
LCAN
Outgoing Request
NACL Outbound SG Outbound
Rules Rules
2
1
1 2
NACL Inbound
Inbound Allowed Rules (Stateless)
(Stateful)
3 3


## Network Access Control List (NACL)

- NACL are like a firewall which control traffic from and to subnets
- One NACL per subnet, new subnets are assigned the Default NACL
- You define NACL Rules:
- Rules have a number (1-32766), higher precedence with a lower number
- First rule match will drive the decision
- Example: if you define #100 ALLOW 10.0.0.10/32 and #200 DENY 10.0.0.10/32, the IP

address will be allowed because 100 has a higher precedence over 200
- The last rule is an asterisk (*) and denies a request in case of no rule match
- AWS recommends adding rules by increment of 100
- Newly created NACLs will deny everything
- NACL are a great way of blocking a specific IP address at the subnet level


## NACLs

Region
NACL NACL
VPC
Internet
www
Public Subnet Private Subnet
Security Group
Internet Router
Gateway
NAT Gateway
Private EC2 Instance
Route Route
Table Security Group Table
Public EC2 Instance
Availability Zone


## Default NACL

- Accepts everything inbound/outbound with the subnets it’s associated with
- Do NOT modify the Default NACL, instead create custom NACLs
    - Default NACL for a VPC that supports IPv4
Inbound Rules
Rule # Type Protocol Port Range Source Allow/Deny
100 All IPv4 Traffic All All 0.0.0.0/0 ALLOW
* All IPv4 Traffic All All 0.0.0.0/0 DENY
Outbound Rules
Rule # Type Protocol Port Range Destination Allow/Deny
100 All IPv4 Traffic All All 0.0.0.0/0 ALLOW
* All IPv4 Traffic All All 0.0.0.0/0 DENY


## Ephemeral Por ts

- For any two endpoints to establish a connection, they must use ports
- Clients connect to a defined port, and expect a response on an ephemeral port
- Different Operating Systems use different port ranges, examples:
- IANA & MS Windows 10 è 49152 – 65535
- Many Linux Kernels è 32768 – 60999
    - Request
Src. IP Src. Port Dest. IP Dest. Port
Payload …
11.22.33.44 50105 55.66.77.88 443
Dest. IP Dest. Port Src. IP Src. Port
Client Payload … Web Server
11.22.33.44 50105 55.66.77.88 443
IP: 11.22.33.44 IP: 55.66.77.88
Ephemeral Port: 50105 Response Fixed Port: 443


## NACL with Ephemeral Por ts

VPC
Web Tier Database Tier
Web Subnet (Public) DB Subnet (Private)
DB Instance
Port 3306
Web-NACL
LCAN-BD
Allow Outbound TCP Allow Inbound TCP
On port 3306 On port 3306
To DB Subnet CIDR From Web Subnet CIDR
Client
Allow Inbound TCP Allow Outbound TCP
Ephemeral
On port 1024-65535 On port 1024-65535
Port
From DB Subnet CIDR To Web Subnet CIDR
https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#nacl-ephemeral-ports


## Create NACL rules for each

target subnets CIDR
VPC
Web Tier Database Tier
Web Subnet - A (Public) DB Subnet – A (Private)
DB Instance
Web Subnet - B (Public) DB Subnet – B (Private)
DB Instance
Web-NACL
LCAN-BD


## Security Group vs. NACLs

Security Group NACL
Operates at the instance level Operates at the subnet level
Supports allow rules only Supports allow rules and deny rules
Stateful: return traffic is automatically allowed, Stateless: return traffic must be explicitly allowed by
regardless of any rules rules (think of ephemeral ports)
All rules are evaluated before deciding whether to Rules are evaluated in order (lowest to highest) when
allow traffic deciding whether to allow traffic, first match wins
Applies to an EC2 instance when specified by Automatically applies to all EC2 instances in the
someone subnet that it’s associated with
NACL Examples: https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html


## VPC Peering

- Privately connect two VPCs using AWS’
    - network VPC - A
- Make them behave as if they were in the
    - same network
VPC Peering
(A – B)
- Must not have overlapping CIDRs
- VPC Peering connection is NOT transitive
    - VPC Peering
VPC - B
(must be established for each VPC that (A – C)
need to communicate with one another)
VPC Peering
- You must update route tables in each VPC’s
    - (B – C)
subnets to ensure EC2 instances can
communicate with each other
VPC - C


## VPC Peering – Good to know

- You can create VPC Peering connection between VPCs in different AWS
    - accounts/regions
- You can reference a security group in a peered VPC (works cross
    - accounts – same region)
Account ID


## VPC Peering

Region
NACL NACL
VPC
Internet
www
Public Subnet Private Subnet
Security Group
Internet Router
Gateway
NAT Gateway
Private EC2 Instance
Route Route
Table Security Group Table
VPC Peering Public EC2 Instance
Connections
Availability Zone


## VPC Endpoints

Amazon
DynamoDB
Region
NACL NACL
VPC
Internet
www
Public Subnet Private Subnet
CloudWatch
Security Group
Internet Router
Gateway
NAT Gateway
Private EC2 Instance S3
Route Route
Table Security Group Table VPC
Endpoint
VPC Peering Public EC2 Instance
Connections
Availability Zone


## VPC Endpoints (AWS PrivateLink)

Amazon SNS
- Every AWS service is publicly exposed
    - (public URL)
www
- VPC Endpoints (powered by AWS
    - Region
Internet
PrivateLink) allows you to connect to AWS Gateway
VPC
services using a private network instead of
Public Subnet
using the public Internet
NAT
EC2 Instance
- They’re redundant and scale horizontally Gateway
- They remove the need of IGW, NATGW, … Private Subnet Option 1
    - to access AWS Services
EC2 Instance
Option 2
- In case of issues:
    - VPC Endpoint
- Check DNS Setting Resolution in your VPC
- Check Route Tables
    - Amazon SNS


## Types of Endpoints

Region
- Interface Endpoints (powered by PrivateLink) VPC
    - Private Subnet
- Provisions an ENI (private IP address) as an entry
    - VPC Endpoint
point (must attach a Security Group) (Interface)
EC2 Instance
ENI (PrivateLink)
- Supports most AWS services
- $ per hour + $ per GB of data processed
    - Amazon SNS
- Gateway Endpoints
- Provisions a gateway and must be used as a
    - Region
target in a route table (does not use security
VPC
groups)
Private Subnet
- Supports both S3 and DynamoDB
    - VPC Endpoint
- Free EC2 Instance
    - (Gateway)
Amazon
Amazon S3 OR
DynamoDB


## Gateway or Interface Endpoint for S3?

Users
- Gateway is most likely going to be
    - preferred all the time at the exam
- Cost: free for Gateway, $ for
    - AWS Cloud
interface endpoint S2S VPN Direct Connect
Region
- Interface Endpoint is preferred
    - VPC
access is required from on-
Interface
premises (Site to Site VPN or
In-VPC
Endpoint
Apps
Direct Connect), a different VPC
or a different region
Gateway
Endpoint
PrivateLink Amazon S3


## Lambda in VPC accessing DynamoDB

- DynamoDB is a public service
    - AWS Cloud
from AWS
Public subnet
- Option 1: Access from the public
    - DynamoDB
internet
NAT IGW
- Because Lambda is in a VPC, it
    - needs a NAT Gateway in a public
subnet and an internet gateway
Private subnet
- Option 2 (better & free): Access
    - VPC Gateway Endpoint
from the private VPC network
For DynamoDB
- Deploy a VPC Gateway endpoint
    - for DynamoDB
- Change the Route Tables


## VPC Flow Logs

- Capture information about IP traffic going into your interfaces:
- VPC Flow Logs
- Subnet Flow Logs
- Elastic Network Interface (ENI) Flow Logs
- Helps to monitor & troubleshoot connectivity issues
- Flow logs data can go to S3, CloudWatch Logs, and Kinesis Data Firehose
- Captures network information from AWS managed interfaces too: ELB,

RDS, ElastiCache, Redshift, WorkSpaces, NATGW, Transit Gateway…


## VPC Flow Logs

VPC Flow Logs
Region
NACL NACL
VPC
Internet
www
Public Subnet Private Subnet
CloudWatch
Security Group
Internet Router
Gateway
NAT Gateway
Amazon
Private EC2 Instance S3
DynamoDB
Route Route
Table Security Group Table VPC
Endpoint
VPC Peering Public EC2 Instance
Connections
Availability Zone


## VPC Flow Logs Syntax

version interface-id dstaddr dstport packets start action
account-id srcaddr srcport protocol bytes end log-status
- srcaddr & dstaddr – help identify problematic IP
- srcport & dstport – help identity problematic ports
- Action – success or failure of the request due to Security Group / NACL
- Can be used for analytics on usage patterns, or malicious behavior
- Query VPC flow logs using Athena on S3 or CloudWatch Logs Insights
- Flow Logs examples: https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs-
    - records-examples.html


## VPC Flow Logs – Troubleshoot SG & NACL issues

Subnet
Security Group
EC2 Instance
LCAN
Subnet
NACL Inbound SG Inbound Security Group
Rules Rules
NACL Outbound Outbound Allowed
Rules (Stateless) EC2 Instance (Stateful)
LCAN
Look at the “ACTION” field
Incoming Requests Outgoing Requests
- Inbound REJECT => NACL or SG • Outbound REJECT => NACL or SG
- Inbound ACCEPT, Outbound REJECT => • Outbound ACCEPT, Inbound REJECT =>
    - NACL NACL
NACL Outbound SG Outbound
Rules Rules
NACL Inbound Inbound Allowed
Rules (Stateless) (Stateful)


## VPC Flow Logs – Architectures

Top-10 IP addresses
CloudWatch VPC Flow Logs CloudWatch Logs
Contributor Insights
Alert
Metric Filter
SSH, RDP…
VPC Flow Logs CloudWatch Logs CW Alarm Amazon SNS
Amazon Amazon
VPC Flow Logs S3 Bucket
Athena QuickSight


## VPC Flow Logs – CloudWatch Permissions

- IAM Service Role associated with VPC Flow Logs must have the required
    - permissions to publish logs to CloudWatch Logs
- logs:CreateLogGroup, logs:CreateLogStream, or logs:PutLogEvents
    - IAM Service Role
logs
VPC Flow Logs CloudWatch Logs


## AWS Site-to-Site VPN

VPC Flow Logs
Region
NACL NACL
VPC
Internet
Corporate
www Public Subnet Private Subnet Data Center
CloudWatch
Security Group
Internet Router
Gateway
NAT Gateway
Amazon
DynamoDB Private EC2 Instance S3
Route Route Server
Customer
Table Security Group Table Security Group VPC Gateway
Endpoints
S2S VPN
Connection
VPC Peering Public EC2 Instance Private EC2 Instance
Connections VPN
Gateway
Availability Zone


## AWS Site-to-Site VPN

- Virtual Private Gateway (VGW)
- VPN concentrator on the AWS side of the VPN connection
- VGW is created and attached to the VPC from which you want to create the
    - Site-to-Site VPN connection
- Possibility to customize the ASN (Autonomous System Number)
- Customer Gateway (CGW)
- Software application or physical device on customer side of the VPN connection
- https://docs.aws.amazon.com/vpn/latest/s2svpn/your-cgw.html#DevicesTested


## Site-to-Site VPN Connections

Route Table
(Route Propagation enabled)
VPC
Private Subnet
- Customer Gateway Device (On-premises)
    - Security Group
- What IP address to use?
- Public Internet-routable IP address for your Customer
    - Gateway device
Virtual Private
- If it’s behind a NAT device that’s enabled for NAT
    - Gateway
traversal (NAT-T), use the public IP address of the NAT
device
- Important step: enable Route Propagation for
    - OR Customer
the Virtual Private Gateway in the route table
Gateway
that is associated with your subnets (Public IP)
NAT Device
- If you need to ping your EC2 instances from (Public IP)
    - Corporate Data Center
on-premises, make sure you add the ICMP
protocol on the inbound of your security
groups Customer
Gateway
(Private IP)
Server


## AWS VPN CloudHub

- Provide secure communication between

multiple sites, if you have multiple VPN VPC Customer Network
connections
Availability Zone
- Low-cost hub-and-spoke model for Customer
    - Private Subnet 1
primary or secondary network connectivity Gateway
between different locations (VPN only)
Customer Network
EC2 Instances
- It’s a VPN connection so it goes over the
    - public Internet
Availability Zone
Virtual Customer
Gateway
Private
- To set it up, connect multiple VPN Private Subnet 2
    - Gateway
connections on the same VGW, setup
(VGW)
dynamic routing and configure route tables Customer Network
EC2 Instances
Customer
Gateway


## Direct Connect (DX)

- Provides a dedicated private connection from a remote network to your VPC
- Dedicated connection must be setup between your DC and AWS Direct
    - Connect locations
- You need to setup a Virtual Private Gateway on your VPC
- Access public resources (S3) and private (EC2) on same connection
- Use Cases:
- Increase bandwidth throughput - working with large data sets – lower cost
- More consistent network experience - applications using real-time data feeds
- Hybrid Environments (on prem + cloud)
- Supports both IPv4 and IPv6


## Direct Connect Diagram

Region
(us-east-1)
VPC Corporate
data center
Private Subnet
VLAN 1
VLAN 2
Virtual Private Gateway AWS Direct Customer or Customer
Connect Endpoint partner router router/firewall
EC2 Instances
Customer or
AWS Cage partner cage
AWS Direct Connect Location Customer Network
Amazon Glacier Amazon S3
Private virtual interface
Public virtual interface


## Direct Connect Gateway

- If you want to setup a Direct Connect to one or more VPC in many

different regions (same account), you must use a Direct Connect Gateway
Region Region
(us-east-1) (us-west-1)
VPC VPC
Customer network
10.0.0.0/16 172.16.0.0/16
Private virtual
Private virtual
interface
interface
AWS Direct
Private virtual
interface Connect
connection
Direct Connect Gateway


## Direct Connect – Connection Types

- Dedicated Connections: 1Gbps,10 Gbps and 100 Gbps capacity
- Physical ethernet port dedicated to a customer
- Request made to AWS first, then completed by AWS Direct Connect Partners
- Hosted Connections: 50Mbps, 500 Mbps, to 10 Gbps
- Connection requests are made via AWS Direct Connect Partners
- Capacity can be added or removed on demand
- 1, 2, 5, 10 Gbps available at select AWS Direct Connect Partners
- Lead times are often longer than 1 month to establish a new connection


## Direct Connect – Encryption

Region
- Data in transit is not encrypted but is
    - (us-east-1)
private
VPC
Availability Zone Corporate
(us-east-1a)
- AWS Direct Connect + VPN data center
    - Private Subnet 1
provides an IPsec-encrypted private
Client Client
connection
EC2 Instances
Availability Zone
- Good for an extra level of security,
    - (us-east-1b) AWS Direct VPN Customer
Connect Endpoint Connection router/firewall
but slightly more complex to put in
Private Subnet 2
place
AWS Direct
Connect Location Customer Network
EC2 Instances


## Direct Connect - Resiliency

High Resiliency for Critical Workloads Maximum Resiliency for Critical Workloads
Corporate Corporate
data center data center
Region Region
AWS Direct
Connect Location - 1 AWS Direct
Connect Location - 1
Corporate
Corporate
data center
data center
AWS Direct
Connect Location - 2
AWS Direct
Connect Location - 2
One connection at multiple locations Maximum resilience is achieved by separate connections
terminating on separate devices in more than one location.


## Site-to-Site VPN connection as a backup

- In case Direct Connect fails, you can set up a backup Direct Connect
    - connection (expensive), or a Site-to-Site VPN connection
AWS Cloud
Direct Connect
Primary Connection
VPC
Site-to-Site VPN
Corporate DC
Backup Connection


## Network topologies can become complicated

VPC Peering
VPN Connection Connection
Customer Gateway Amazon VPC Amazon VPC
VPC Peering
Connection
VPN Connection
VPC Peering VPC Peering
Connection Connection
Direct Connect
Gateway
VPN Connection VPC Peering
Amazon VPC Amazon VPC
Connection


## Transit Gateway

- For having transitive peering between thousands of VPC and
    - on-premises, hub-and-spoke (star) connection
AWS Direct
Connect Gateway
- Regional resource, can work cross-region
- Share cross-account using Resource Access Manager (RAM)
- You can peer Transit Gateways across regions
    - Amazon VPC Amazon VPC
- Route Tables: limit which VPC can talk with other VPC
- Works with Direct Connect Gateway, VPN connections
    - Transit
- Supports IP Multicast (not supported by any other AWS
    - Gateway
service)
Amazon VPC Amazon VPC
VPN Connection
Customer Gateway


## Transit Gateway: Site-to-Site VPN ECMP

- ECMP = Equal-cost multi-path
    - routing
VPC
- Routing strategy to allow to V
    - P
C
a
tta
forward a packet over multiple c h m Corporate data center
best path
VPC VPC
attachm
e
e
n
n
t
t
VPN attachment 172.16.0.0/16
- Use case: create multiple Site-
    - VPC
VPC
attach m
n
e
t
nt
e
to-Site VPN connections to m
h
c
a
att
AWS Transit Gateway
increase the bandwidth of your C
P
V
VPC
connection to AWS


## Transit Gateway: throughput with ECMP

VPN to transit gateway
VPN to virtual private gateway
VPC VPC
1x = 1x 1x = 1x VPC
VPC
VPC
1x = 1.25 Gbps 1x = 2.5 Gbps (ECMP) – 2 tunnels used
2x = 5.0 Gbps (ECMP)
3x = 7.5 Gbps (ECMP)
VPN connection
(2 tunnels)
per GB of TGW
processed data


## Transit Gateway – Share Direct Connect

between multiple accounts
AWS Cloud
Corporate
Region
data center
Account 1
Clients Clients
VPC
Transit VIF VLAN
Transit Direct AWS Direct Customer
Gateway Connect Connect endpoint router/firewall
Account 2
Gateway
VPC
AWS Direct Servers
Connect Location
You can use AWS Resource Access Manager to share Transit
Gateway with other accounts.


## VPC – Traffic Mirroring

Source A Source B
- Allows you to capture and inspect network
    - traffic in your VPC
- Route the traffic to security appliances that
    - you manage Inbound & Inbound &
Outbound traffic Outbound
- Capture the traffic
    - traffic
- From (Source) – ENIs
- To (Targets) – an ENI or a Network Load Balancer
    - Traffic Mirroring
- Capture all packets or capture the packets of
    - (filter traffic, optional)
your interest (optionally, truncate packets)
Network Load
- Source and Target can be in the same VPC or
    - Balancer
different VPCs (VPC Peering)
- Use cases: content inspection, threat
    - Auto Scaling group
monitoring, troubleshooting, …
EC2 instances with Security Appliances


## What is IPv6?

- IPv4 designed to provide 4.3 Billion addresses (they’ll be exhausted soon)
- IPv6 is the successor of IPv4
    - ,-
- IPv6 is designed to provide 3.4 × 10 unique IP addresses
- Every IPv6 address in AWS is public and Internet-routable (no private range)
- Format è x.x.x.x.x.x.x.x (x is hexadecimal, range can be from 0000 to ffff)
- Examples:
- 2001:db8:3333:4444:5555:6666:7777:8888
- 2001:db8:3333:4444:cccc:dddd:eeee:ffff
- :: è all 8 segments are zero
- 2001:db8:: è the last 6 segments are zero
- ::1234:5678 è the first 6 segments are zero
- 2001:db8::1234:5678 è the middle 4 segments are zero


## IPv6 in VPC

Internet
- IPv4 cannot be disabled for your VPC and
    - subnets
- You can enable IPv6 (they’re public IP addresses)
    - to operate in dual-stack mode
Internet
Gateway
VPC
IPv4 & IPv6
- Your EC2 instances will get at least a private
    - internal IPv4 and a public IPv6
- They can communicate using either IPv4 or IPv6
    - EC2 Instance
to the internet through an Internet Gateway
(Private IP: 10.0.0.5)
(IPv6: 2001:db8::ff00:42:8329)


## IPv4 Troubleshooting

User
- IPv4 cannot be disabled for your VPC
    - and subnets
- So, if you cannot launch an EC2 instance create
    - in your subnet
VPC
- It’s not because it cannot acquire an IPv6
    - (IPv4: 192.168.0.0/24)
(the space is very large)
(IPv4: 10.0.0.0/24)
(IPv6: 2001:db8:1234:5678::/56)
- It’s because there are no available IPv4 in
    - your subnet …
192.168.0.10 192.168.0.15
- Solution: create a new IPv4 CIDR in
    - your subnet
10.0.0.35


## Egress-only Internet Gateway

- Used for IPv6 only
    - Internet
- (similar to a NAT Gateway but for IPv6)
    - can’t initiate
initiate connections
connections from
from both sides
Internet
- Allows instances in your VPC outbound
    - connections over IPv6 while preventing
VPC
the internet to initiate an IPv6 connection
Internet Egress-only
Gateway Internet Gateway
to your instances
- You must update the Route Tables
    - Public Subnet Private Subnet
IPv6: 2001:db8::b1c2 IPv6: 2001:db8::e1c3


## IPv6 Routing

Route Table
(Public Subnet)
Region
Destination Target
10.0.0.0/16 local
VPC 2001:db8:1234:1a00::/56 local
NAT Gateway
(IPv4: 10.0.0.0/16)
0.0.0.0/0 igw-id
(IPv6: 2001:db8:1234:1a00::/56) (IPv4)
::/0 igw-id
Public Subnet
(IPv4: 10.0.0.0/24) EIP: 198.51.100.1
(IPv6: 2001:db8:1234:1a00::/64)
Internet
Private IPv4: 10.0.0.5
EIP: 198.51.100.1 Gateway Internet
IPv6: 2001:db8:1234:1a00::123 Web server (IPv4 & IPv6)
Private Subnet
Route Table
(IPv4: 10.0.1.0/24)
(IPv6: 2001:db8:1234:1a02::/64) Egress-only (Private Subnet)
Internet Gateway Destination Target
Private IPv4: 10.0.1.5
(IPv6)
IPv6: 2001:db8:1234:1a02::456
10.0.0.0/16 local
Server
2001:db8:1234:1a00::/56 local
0.0.0.0/0 nat-gateway-id
::/0 eigw-id


## VPC Section Summar y (1/3)

- CIDR – IP Range
- VPC – Virtual Private Cloud => we define a list of IPv4 & IPv6 CIDR
- Subnets – tied to an AZ, we define a CIDR
- Internet Gateway – at the VPC level, provide IPv4 & IPv6 Internet Access
- Route Tables – must be edited to add routes from subnets to the IGW, VPC Peering
    - Connections, VPC Endpoints, …
- Bastion Host – public EC2 instance to SSH into, that has SSH connectivity to EC2
    - instances in private subnets
- NAT Instances – gives Internet access to EC2 instances in private subnets. Old, must

be setup in a public subnet, disable Source / Destination check flag
- NAT Gateway – managed by AWS, provides scalable Internet access to private EC2
    - instances, when the target is an IPv4 address


## VPC Section Summar y (2/3)

- NACL – stateless, subnet rules for inbound and outbound, don’t forget Ephemeral
    - Ports
- Security Groups – stateful, operate at the EC2 instance level
- VPC Peering – connect two VPCs with non overlapping CIDR, non-transitive
- VPC Endpoints – provide private access to AWS Services (S3, DynamoDB,
    - CloudFormation, SSM) within a VPC
- VPC Flow Logs – can be setup at the VPC / Subnet / ENI Level, for ACCEPT and

REJECT traffic, helps identifying attacks, analyze using Athena or CloudWatch Logs
Insights
- Site-to-Site VPN – setup a Customer Gateway on DC, a Virtual Private Gateway on
    - VPC, and site-to-site VPN over public Internet
- AWS VPN CloudHub – hub-and-spoke VPN model to connect your sites


## VPC Section Summar y (3/3)

- Direct Connect – setup a Virtual Private Gateway on VPC, and establish a
    - direct private connection to an AWS Direct Connect Location
- Direct Connect Gateway – setup a Direct Connect to many VPCs in different
    - AWS regions
- AWS PrivateLink / VPC Endpoint Services:
- Connect services privately from your service VPC to customers VPC
- Doesn’t need VPC Peering, public Internet, NAT Gateway, Route Tables
- Must be used with Network Load Balancer & ENI
- ClassicLink – connect EC2-Classic EC2 instances privately to your VPC
- Transit Gateway – transitive peering connections for VPC, VPN & DX
- Traffic Mirroring – copy network traffic from ENIs for further analysis
- Egress-only Internet Gateway – like a NAT Gateway, but for IPv6 targets


## Networking Costs in AWS per GB - Simplified

Region Region
- Use Private IP
    - Availability Zone Availability Zone Availability Zone
instead of Public
IP for good
Free for traffic in
savings and
Free if using better network
private IP
performance
$0.01 if $0.02
Using private IP Inter-region
- Use same AZ for
    - maximum savings
(at the cost of
$0.02 if using
high availability)
Public IP / Elastic IP


## Minimizing egress traffic network cost

- Egress traffic: outbound
    - Egress cost is high
traffic (from AWS to
outside)
AWS Cloud Corporate data center
- Ingress traffic: inbound
    - DB Query
Query Results
traffic - from outside to
100 MB
50 KB
AWS (typically free)
- Try to keep as much
    - Application
Database
internet traffic within
AWS to minimize costs
Egress cost is minimized
- Direct Connect location
    - AWS Cloud Corporate data center
that are co-located in
the same AWS Region
Query Results
DB Query
result in lower cost for
50 KB
100 MB
egress network
Application
Database


## S3 Data Transfer Pricing – Analysis for USA

- S3 ingress: free
    - internet
- S3 to Internet: $0.09 per GB
- S3 Transfer Acceleration:
    - $0.09
- Faster transfer times (50 to 500% better)
- Additional cost on top of Data Transfer
    - Transfer acceleration +$0.04
Pricing: +$0.04 to $0.08 per GB
- S3 to CloudFront: $0.00 per GB
    - Edge location
- CloudFront to Internet: $0.085 per GB
    - (slightly cheaper than S3)
$0.00
- Caching capability (lower latency)
- Reduce costs associated with S3 Requests Replication $0.02
    - $0.085
Pricing (7x cheaper with CloudFront)
- S3 Cross Region Replication: $0.02 per GB
    - CloudFront


## Pricing:

NAT Gateway vs Gateway VPC Endpoint
Region
(us-east-1)
$0.045 NAT Gateway / hour
VPC
$0.045 NAT Gateway data processed / GB
(10.0.0.0/16)
Subnet 1 route table
$0.09 Data transfer out to S3 (cross-region)
Destination Target Private subnet 1 Public subnet
$0.00 Data transfer out to S3 (same-region)
(10.0.0.0/24)
10.0.0.0/16 Local
0.0.0.0/0 igw-id
Internet
EC2 Instance NAT Gateway
Internet
Subnet 2 route table Gateway
Destination Target Private subnet 2
(10.0.1.0/24)
10.0.0.0/16 Local
pl-id for vpce-id
No cost for using Gateway Endpoint.
Amazon S3
$0.01 Data transfer in/out (same-
EC2 Instance VPC Endpoint S3 Bucket region)


## Network Protection on AWS

- To protect network on AWS, we’ve seen
- Network Access Control Lists (NACLs)
- Amazon VPC security groups
- AWS WAF (protect against malicious requests)
- AWS Shield & AWS Shield Advanced
- AWS Firewall Manager (to manage them across accounts)
- But what if we want to protect in a sophisticated way our entire VPC?


## AWS Network Firewall

internet
- Protect your entire Amazon VPC
    - AWS Network Firewall
- From Layer 3 to Layer 7 protection
- Any direction, you can inspect
- VPC to VPC traffic VPC
    - Direct Connect
- Outbound to internet
- Inbound from internet
    - Private subnet
- To / from Direct Connect & Site-to-Site VPN
    - Corporate DC
- Internally, the AWS Network Firewall uses
    - VPN connection
the AWS Gateway Load Balancer
- Rules can be centrally managed cross-
    - account by AWS Firewall Manager to apply
to many VPCs
Peered VPC


## Network Firewall – Fine Grained Controls

- Supports 1000s of rules
- IP & port - example: 10,000s of IPs filtering
- Protocol – example: block the SMB protocol for outbound communications
- Stateful domain list rule groups: only allow outbound traffic to *.mycorp.com or third-party
    - software repo
- General pattern matching using regex
- Traffic filtering: Allow, drop, or alert for the traffic that matches the rules
- Active flow inspection to protect against network threats with intrusion-prevention

capabilities (like Gateway Load Balancer, but all managed by AWS)
- Send logs of rule matches to Amazon S3, CloudWatch Logs, Kinesis Data Firehose
