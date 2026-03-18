# AWS Storage Extras


## AWS Snowball

- Highly-secure, portable devices to collect and process data at the
    - edge, and migrate data into and out of AWS
- Helps migrate up to Petabytes of data
    - Device Compute Memory Storage (SSD)
Snowball Edge Storage Optimized 104 vCPUs 416 GB 210 TB
Snowball Edge Compute Optimized 104 vCPUs 416 GB 28 TB
Snowball Edge


## Data Migrations with Snowball

Challenges:
- Limited connectivity
    - Time to Transfer
- Limited bandwidth
    - 100 Mbps 1Gbps 10Gbps
10 TB 12 days 30 hours 3 hours • High network cost
100 TB 124 days 12 days 30 hours • Shared bandwidth (can’t
maximize the line)
1 PB 3 years 124 days 12 days
- Connection stability
    - AWS Snowball: offline devices to perform data migrations
If it takes more than a week to transfer over the network, use Snowball devices!


## Diagrams

- Direct upload to S3:
    - www: 10Gbit/s
client Amazon S3
bucket
- With Snowball:
    - ship
AWS AWS import/ Amazon S3
client
Snowball Snowball export bucket


## What is Edge Computing?

- Process data while it’s being created on an edge location
- A truck on the road, a ship on the sea, a mining station underground...
- These locations may have limited internet and no access to computing power
- We setup a Snowball Edge device to do edge computing
- Snowball Edge Compute Optimized (dedicated for that use case) & Storage Optimized
- Run EC2 Instances or Lambda functions at the edge
- Use cases: preprocess data, machine learning, transcoding media


## Solution Architecture: Snowball into Glacier

- Snowball cannot import to Glacier directly
- You must use Amazon S3 first, in combination with an S3 lifecycle policy
    - import S3 lifecycle policy
Snowball Amazon S3 Amazon Glacier


## Amazon FSx – Overview

- Launch 3rd party high-performance file systems on AWS
- Fully managed service
    - FSx for
FSx for Lustre
NetApp ONTAP
FSx for Windows FSx for
File Server OpenZFS


## Amazon FSx for Windows (File Ser ver)

- FSx for Windows is a fully managed Windows file system share drive
- Supports SMB protocol & Windows NTFS
- Microsoft Active Directory integration, ACLs, user quotas
- Can be mounted on Linux EC2 instances
- Supports Microsoft's Distributed File System (DFS) Namespaces (group files across multiple FS)
- Scale up to 10s of GB/s, millions of IOPS, 100s PB of data
- Storage Options:
- SSD – latency sensitive workloads (databases, media processing, data analytics, …)
- HDD – broad spectrum of workloads (home directory, CMS, …)
- Can be accessed from your on-premises infrastructure (VPN or Direct Connect)
- Can be configured to be Multi-AZ (high availability)
- Data is backed-up daily to S3


## Amazon FSx for Lustre

- Lustre is a type of parallel distributed file system, for large-scale computing
- The name Lustre is derived from “Linux” and “cluster
- Machine Learning, High Performance Computing (HPC)
- Video Processing, Financial Modeling, Electronic Design Automation
- Scales up to 100s GB/s, millions of IOPS, sub-ms latencies
- Storage Options:
- SSD – low-latency, IOPS intensive workloads, small & random file operations
- HDD – throughput-intensive workloads, large & sequential file operations
- Seamless integration with S3
- Can “read S3” as a file system (through FSx)
- Can write the output of the computations back to S3 (through FSx)
- Can be used from on-premises servers (VPN or Direct Connect)


## FSx Lustre - File System Deployment Options

Region
- Scratch File System
    - Availability Zone 1 Availability Zone 2
- Temporary storage ENI
    - Compute Compute
instances instances
- Data is not replicated (doesn’t persist if file
    - server fails)
- High burst (6x faster, 200MBps per TiB)
    - FSx For Lustre S3 bucket
- Usage: short-term processing, optimize
    - (Scratch file system) (optional data repository)
costs
Region
- Persistent File System
    - Availability Zone 1 Availability Zone 2
- Long-term storage ENI
    - Compute Compute
- Data is replicated within same AZ instances instances
- Replace failed files within minutes
- Usage: long-term processing, sensitive data
    - FSx For Lustre S3 bucket
(Persistent file system) (optional data repository)


## Amazon FSx for NetApp ONTAP

- Managed NetApp ONTAP on AWS
- File System compatible with NFS, SMB, iSCSI protocol
    - Amazon FSx for
- Move workloads running on ONTAP or NAS to AWS
    - NetApp ONTAP FS
- Works with:
- Linux
- Windows
    - NFS, SMB, iSCSI
- MacOS
- VMware Cloud on AWS
- Amazon Workspaces & AppStream 2.0
- Amazon EC2, ECS and EKS
- Storage shrinks or grows automatically
- Snapshots, replication, low-cost, compression and data EC2 ECS EKS
    - de-duplication
- Point-in-time instantaneous cloning (helpful for testing
    - new workloads)
VMware Cloud Amazon Amazon On-premises
on AWS AppStream 2.0 WorkSpaces Server


## Amazon FSx for OpenZFS

- Managed OpenZFS file system on AWS
- File System compatible with NFS (v3, v4, v4.1, v4.2)
    - Amazon FSx
- Move workloads running on ZFS to AWS for OpenZFS
- Works with:
- Linux
- Windows NFS (v3, v4, v4.1, v4.2)
- MacOS
- VMware Cloud on AWS
- Amazon Workspaces & AppStream 2.0
- Amazon EC2, ECS and EKS
- Up to 1,000,000 IOPS with < 0.5ms latency
    - EC2 ECS EKS
- Snapshots, compression and low-cost
- Point-in-time instantaneous cloning (helpful for
    - testing new workloads)
VMware Cloud Amazon Amazon On-premises
on AWS AppStream 2.0 WorkSpaces Server


## Hybrid Cloud for Storage

- AWS is pushing for ”hybrid cloud”
- Part of your infrastructure is on the cloud
- Part of your infrastructure is on-premises
- This can be due to
- Long cloud migrations
- Security requirements
- Compliance requirements
- IT strategy
- S3 is a proprietary storage technology (unlike EFS / NFS), so how do
    - you expose the S3 data on-premises?
- AWS Storage Gateway!


## AWS Storage Cloud Native Options

Block File Object
Amazon EBS EC2 Instance Amazon EFS Amazon FSx Amazon S3 Amazon Glacier
Store


## AWS Storage Gateway

- Bridge between on-premises data and cloud data
- Use cases:
- disaster recovery
- backup & restore
    - Storage Gateway
- tiered storage
- on-premises cache & low-latency files access
- Types of Storage Gateway:
- S3 File Gateway
- Volume Gateway
- Tape Gateway


## Amazon S3 File Gateway

- Configured S3 buckets are accessible using the NFS and SMB protocol
- Most recently used data is cached in the file gateway
- Supports S3 Standard, S3 Standard IA, S3 One Zone A, S3 Intelligent Tiering
- Transition to S3 Glacier using a Lifecycle Policy
- Bucket access using IAM roles for each File Gateway
- SMB Protocol has integration with Active Directory (AD) for user authentication

Corporate AWS Cloud .
Data Center
S3 Standard Lifecycle
HTTPS
S3 Standard-IA policy
S3 One Zone-IA
NFS or SMB
S3 Intelligent-Tiering
S3 Glacier
Application S3 File
Server Gateway


## Volume Gateway

- Block storage using iSCSI protocol backed by S3
- Backed by EBS snapshots which can help restore on-premises volumes!
- Cached volumes: low latency access to most recent data
- Stored volumes: entire dataset is on premise, scheduled backups to S3
    - Corporate AWS Cloud
Data Center
Region
iSCSI HTTPS
Application Volume Gateway S3 Bucket Amazon EBS
Snapshots
Server


## Tape Gateway

- Some companies have backup processes using physical tapes (!)
- With Tape Gateway, companies use the same processes but, in the cloud
- Virtual Tape Library (VTL) backed by Amazon S3 and Glacier
- Back up data using existing tape-based processes (and iSCSI interface)
- Works with leading backup software vendors
    - Corporate AWS Cloud
Data Center
Region
Media
iSCSI Changer HTTPS
Virtual Tapes Archived Tapes
Tape Tape
Backup
stored in stored in
Drive Gateway
Server Amazon S3 Amazon Glacier


## AWS Storage Gateway

On-Premises AWS Cloud
NFS/SMB
Any S3 Storage Class
User/group file shares File Gateway Amazon S3
Including Glacier
local cache excluding Glacier &
Glacier Deep Archive
iSCSI Encryption in Transit
Internet or Direct Connect
Application Server Volume Gateway Amazon S3 AWS EBS
local cache Storage Gateway
Eject from backup application
iSCSI VTL
Tape Archive
Amazon S3
Backup Application Tape Gateway Tape Library Glacier &
Glacier Deep Archive
local cache
Gateway Deployment Options
VM(VMware, Hyper-V, KVM)


## AWS Transfer Family

- A fully-managed service for file transfers into and out of Amazon S3 or
    - Amazon EFS using the FTP protocol
- Supported Protocols
- AWS Transfer for FTP (File Transfer Protocol (FTP))
- AWS Transfer for FTPS (File Transfer Protocol over SSL (FTPS))
- AWS Transfer for SFTP (Secure File Transfer Protocol (SFTP))
- Managed infrastructure, Scalable, Reliable, Highly Available (multi-AZ)
- Pay per provisioned endpoint per hour + data transfers in GB
- Store and manage users’ credentials within the service
- Integrate with existing authentication systems (Microsoft Active Directory,
    - LDAP, Okta, Amazon Cognito, custom)
- Usage: sharing files, public datasets, CRM, ERP, …


## AWS Transfer Family

MS Active Directory
authenticate
LDAP
… AWS Transfer for SFTP
Amazon S3
IAM Role
AWS Transfer for FTPS
Users
Route 53
(FTP client)
(optional) AWS Transfer for FTP
(only within VPC)
Amazon EFS
AWS Transfer Family


## AWS DataSync

- Move large amount of data to and from
- On-premises / other cloud to AWS (NFS, SMB, HDFS, S3 API…) – needs agent
- AWS to AWS (different storage services) – no agent needed
- Can synchronize to:
- Amazon S3 (any storage classes – including Glacier)
- Amazon EFS
- Amazon FSx (Windows, Lustre, NetApp, OpenZFS...)
- Replication tasks can be scheduled hourly, daily, weekly
- File permissions and metadata are preserved (NFS POSIX, SMB…)
- One agent task can use 10 Gbps, can setup a bandwidth limit


## AWS DataSync

NFS / SMB to AWS (S3, EFS, FSx…)
Region
On-Premises
AWS Storage Resources
NFS or SMB TLS S3 Standard S3 Intelligent- S3 Standard-IA
Tiering
NFS or SMB AWS DataSync
AWS S3 One S3 Glacier S3 Glacier
Server Agent Zone-IA Deep Archive
DataSync
AWS Snowcone
(agent pre-installed)
AWS EFS Amazon FSx


## AWS DataSync

Transfer between AWS storage services
Amazon S3 Amazon S3
Amazon EFS Amazon EFS
AWS DataSync
copy data and metadata
between AWS Storage Services
Amazon FSx Amazon FSx


## Storage Comparison

- S3: Object Storage
- S3 Glacier: Object Archival
- EBS volumes: Network storage for one EC2 instance at a time
- Instance Storage: Physical storage for your EC2 instance (high IOPS)
- EFS: Network File System for Linux instances, POSIX filesystem
- FSx for Windows: Network File System for Windows servers
- FSx for Lustre: High Performance Computing Linux file system
- FSx for NetApp ONTAP: High OS Compatibility
- FSx for OpenZFS: Managed ZFS file system
- Storage Gateway: S3 & FSx File Gateway, Volume Gateway (cache & stored), Tape Gateway
- Transfer Family: FTP, FTPS, SFTP interface on top of Amazon S3 or Amazon EFS
- DataSync: Schedule data sync from on-premises to AWS, or AWS to AWS
- Snowcone / Snowball / Snowmobile: to move large amount of data to the cloud, physically
- Database: for specific workloads, usually with indexing and querying
