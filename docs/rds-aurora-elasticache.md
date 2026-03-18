# RDS, Aurora & ElastiCache


## Amazon RDS Overview

- RDS stands for Relational Database Service
- It’s a managed DB service for DB use SQL as a query language.
- It allows you to create databases in the cloud that are managed by AWS
- Postgres
- MySQL
- MariaDB
- Oracle
- Microsoft SQL Server
- IBM DB2
- Aurora (AWS Proprietary database)


## Advantage over using RDS versus deploying

DB on EC2
- RDS is a managed service:
- Automated provisioning, OS patching
- Continuous backups and restore to specific timestamp (Point in Time Restore)!
- Monitoring dashboards
- Read replicas for improved read performance
- Multi AZ setup for DR (Disaster Recovery)
- Maintenance windows for upgrades
- Scaling capability (vertical and horizontal)
- Storage backed by EBS
- BUT you can’t SSH into your instances


## RDS – Storage Auto Scaling

- Helps you increase storage on your RDS DB instance
    - dynamically
- When RDS detects you are running out of free database
    - storage, it scales automatically
- Avoid manually scaling your database storage
    - Application
- You have to set Maximum Storage Threshold (maximum limit
    - for DB storage)
- Automatically modify storage if:
    - Read/Write
- Free storage is less than 10% of allocated storage
- Low-storage lasts at least 5 minutes
- 6 hours have passed since last modification
- Useful for applications with unpredictable workloads
- Supports all RDS database engines
    - Storage


## RDS Read Replicas for read scalability

- Up to 15 Read Replicas
- Within AZ, Cross AZ or
    - Application
Cross Region
- Replication is ASYNC,
    - so reads are eventually
consistent writes
reads reads reads
- Replicas can be
    - promoted to their own
DB
- Applications must
    - update the connection
string to leverage read ASYNC ASYNC
replicas replication replication
RDS DB RDS DB
RDS DB
instance read instance read
instance
replica replica


## RDS Read Replicas – Use Cases

- You have a production database
    - that is taking on normal load Production Reporting
Application Application
- You want to run a reporting
    - application to run some analytics
- You create a Read Replica to run
    - rreeaaddss
reads
the new workload there
- The production application is
    - unaffected
- Read replicas are used for SELECT
    - (=read) only kind of statements
ASYNC
(not INSERT, UPDATE, DELETE)
replication
RDS DB
RDS DB
instance read
instance
replica


## RDS Read Replicas – Network Cost

- In AWS there’s a network cost when data goes from one AZ to another
- For RDS Read Replicas within the same region, you don’t pay that fee
    - Same Region / Different AZ Region/AZ Region/AZ
us-east-1a us-east-1b us-east-1a eu-west-1b
VS
ASYNC ASYNC
Replication Replication
RDS DB RDS DB
RDS DB RDS DB
Same Region instance read Cross-Region instance read
instance instance
Free replica $$$ replica


## RDS Multi AZ (Disaster Recovery)

- SYNC replication
    - Application
- One DNS name – automatic app
    - failover to standby
- Increase availability writes
    - reads
- Failover in case of loss of AZ, loss of
    - network, instance or storage failure
One DNS name – automatic failover
- No manual intervention in apps
- Not used for scaling
- Note: The Read Replicas be setup as
    - SYNC
Multi AZ for Disaster Recovery (DR)
replication
RDS DB
RDS Master DB
instance standby
instance (AZ A)
(AZ B)


## RDS – From Single-AZ to Multi-AZ

- Zero downtime operation (no
    - RDS DB Standby DB
need to stop the DB)
instance
- Just click on “modify” for the
    - database
- The following happens internally:
    - SYNC
Replication
- A snapshot is taken
- A new DB is restored from the
    - snapshot in a new AZ
restore
snapshot
- Synchronization is established
    - between the two databases
DB snapshot


## RDS Custom

User
- Managed Oracle and Microsoft SQL Server Database with OS and
    - database customization
apply
- RDS: Automates setup, operation, and scaling of database in AWS
    - SSH
cstomizations
- Custom: access to the underlying database and OS so you can
- Configure settings
- Install patches
    - EC2 Instance
- Enable native features
- Access the underlying EC2 Instance using SSH or SSM Session Manager Automation
    - Mode disabled
- De-activate Automation Mode to perform your customization,
    - better to take a DB snapshot before
- RDS vs. RDS Custom
- RDS: entire database and the OS to be managed by AWS
- RDS Custom: full admin access to the underlying OS and the database


## Amazon Aurora

- Aurora is a proprietary technology from AWS (not open sourced)
- Postgres and MySQL are both supported as Aurora DB (that means your

drivers will work as if Aurora was a Postgres or MySQL database)
- Aurora is “AWS cloud optimized” and claims 5x performance improvement

over MySQL on RDS, over 3x the performance of Postgres on RDS
- Aurora storage automatically grows in increments of 10GB, up to 256 TB.
- Aurora can have up to 15 replicas and the replication process is faster than
    - MySQL (sub 10 ms replica lag)
- Failover in Aurora is instantaneous. It’s HA (High Availability) native.
- Aurora costs more than RDS (20% more) – but is more efficient


## Aurora High Availability and Read Scaling

- 6 copies of your data across 3 AZ:
    - AZ 1 AZ 2 AZ 3
- 4 copies out of 6 needed for writes
- 3 copies out of 6 need for reads
- Self healing with peer-to-peer replication
- Storage is striped across 100s of volumes
    - W
R R R R R
- One Aurora Instance takes writes (master)
- Automated failover for master in less than Shared storage Volume
    - Replication + Self Healing + Auto Expanding
30 seconds
- Master + up to 15 Aurora Read Replicas
    - serve reads
- Support for Cross Region Replication


## Aurora DB Cluster

client
Writer Endpoint Reader Endpoint
Pointing to the master Connection Load Balancing
Auto Scaling
W R R R R R
Shared storage Volume
Auto Expanding from 10G to 256 TB


## Features of Aurora

- Automatic fail-over
- Backup and Recovery
- Isolation and security
- Industry compliance
- Push-button scaling
- Automated Patching with Zero Downtime
- Advanced Monitoring
- Routine Maintenance
- Backtrack: restore data at any point of time without using backups


## Aurora Replicas - Auto Scaling

Client
Many Requests
Writer Endpoint Reader Endpoint Endpoint Extended
CPU CPU
Usage Usage
Replicas Auto Scaling
W
R R R R
Shared Storage Volume


## Aurora – Custom Endpoints

- Define a subset of Aurora Instances as a Custom Endpoint
- Example: Run analytical queries on specific replicas
- The Reader Endpoint is generally not used after defining Custom Endpoints
    - Analytical Queries
Queries
Client
Writer Endpoint Reader Endpoint Custom Endpoint
db.r3.large db.r3.large db.r5.2xlarge db.r5.2xlarge
W
R R R R
Shared Storage Volume


## Aurora Serverless

- Automated database Client
    - instantiation and auto-
scaling based on actual
usage
- Good for infrequent, Proxy Fleet
    - (managed by Aurora)
intermittent or
unpredictable workloads
- No capacity planning
    - needed
- Pay per second, can be
    - more cost-effective
Shared storage Volume


## Global Aurora

us-east-1 - PRIMARY region
- Aurora Cross Region Read Replicas:
- Useful for disaster recovery
- Simple to put in place
    - Applications
- Aurora Global Database (recommended):
    - Read / Write
- 1 Primary Region (read / write)
- Up to 10 secondary (read-only) regions, replication lag is less replication
    - than 1 second
eu-west-1 - SECONDARY region
- Up to 16 Read Replicas per secondary region
- Helps for decreasing latency
- Promoting another region (for disaster recovery) has an RTO
    - of < 1 minute
- Typical cross-region replication takes less than 1 second
    - Applications
Read Only


## Aurora Machine Learning

- Enables you to add ML-based predictions to
    - Application
your applications via SQL
SQL query query results
- Simple, optimized, and secure integration
    - (Recommended products?) (red shirt, blue …)
between Aurora and AWS ML services
Amazon Aurora
- Supported services
- Amazon SageMaker (use with any ML model)
    - data
predictions
(user’s profile,
- Amazon Comprehend (for sentiment analysis) (red shirt,
    - shopping history, …)
blue pants, …)
- You don’t need to have ML experience
- Use cases: fraud detection, ads targeting,
    - sentiment analysis, product recommendations
Amazon Amazon
SageMaker Comprehend


## Babelfish for Aurora PostgreSQL

- Allows Aurora PostgreSQL to
    - Application Application
understand commands targeted for
SQL Server Client Driver PostgreSQL Driver
MS SQL Server
(e.g., T-SQL)
- Therefore Microsoft SQL Server
    - based applications can work on
T-SQL PL/pgSQL
Aurora PostgreSQL
- Requires no to little code changes
    - T-SQL
(using the same MS SQL Server client
driver)
Babelfish PostgreSQL
- The same applications can be used
    - after a migration of your database
migrate
(using AWS SCT and DMS)
Aurora PostgreSQL


## RDS Backups

- Automated backups:
- Daily full backup of the database (during the backup window)
- Transaction logs are backed-up by RDS every 5 minutes
- => ability to restore to any point in time (from oldest backup to 5 minutes ago)
- 1 to 35 days of retention, set 0 to disable automated backups
- Manual DB Snapshots
- Manually triggered by the user
- Retention of backup for as long as you want
- Trick: in a stopped RDS database, you will still pay for storage. If you plan on

stopping it for a long time, you should snapshot & restore instead


## Aurora Backups

- Automated backups
- 1 to 35 days (cannot be disabled)
- point-in-time recovery in that timeframe
- Manual DB Snapshots
- Manually triggered by the user
- Retention of backup for as long as you want


## RDS & Aurora Restore options

- Restoring a RDS / Aurora backup or a snapshot creates a new database
- Restoring MySQL RDS database from S3
- Create a backup of your on-premises database
- Store it on Amazon S3 (object storage)
- Restore the backup file onto a new RDS instance running MySQL
- Restoring MySQL Aurora cluster from S3
- Create a backup of your on-premises database using Percona XtraBackup
- Store the backup file on Amazon S3
- Restore the backup file onto a new Aurora cluster running MySQL


## Aurora Database Cloning

- Create a new Aurora DB Cluster from an
    - existing one
- Faster than snapshot & restore
- Uses copy-on-write protocol
- Initially, the new DB cluster uses the same data clone
    - volume as the original DB cluster (fast and efficient
– no copying is needed)
- When updates are made to the new DB cluster

data, then additional storage is allocated and data is Production Aurora Staging Aurora
copied to be separated
- Very fast & cost-effective
- Useful to create a “staging” database from a
    - “production” database without impacting the
production database


## RDS & Aurora Security

- At-rest encryption:
- Database master & replicas encryption using AWS KMS – must be defined as launch time
- If the master is not encrypted, the read replicas cannot be encrypted
- To encrypt an un-encrypted database, go through a DB snapshot & restore as encrypted
- In-flight encryption: TLS-ready by default, use the AWS TLS root certificates client-side
- IAM Authentication: IAM roles to connect to your database (instead of username/pw)
- Security Groups: Control Network access to your RDS / Aurora DB
- No SSH available except on RDS Custom
- Audit Logs can be enabled and sent to CloudWatch Logs for longer retention


## Amazon RDS Proxy

VPC
- Fully managed database proxy for RDS
    - Lambda functions
- Allows apps to pool and share DB connections
    - …
established with the database
- Improving database efficiency by reducing the stress IAM
    - on database resources (e.g., CPU, RAM) and Authentication
minimize open connections (and timeouts)
- Serverless, autoscaling, highly available (multi-AZ)
- Reduced RDS & Aurora failover time by up 66% Private subnet
- Supports RDS (MySQL, PostgreSQL, MariaDB, MS
    - RDS Proxy
SQL Server) and Aurora (MySQL, PostgreSQL)
- No code changes required for most apps
- Enforce IAM Authentication for DB, and securely
    - store credentials in AWS Secrets Manager
- RDS Proxy is never publicly accessible (must be
    - RDS DB
accessed from VPC)
Instance


## Amazon ElastiCache Overview

- The same way RDS is to get managed Relational Databases…
- ElastiCache is to get managed Redis or Memcached
- Caches are in-memory databases with really high performance, low
    - latency
- Helps reduce load off of databases for read intensive workloads
- Helps make your application stateless
- AWS takes care of OS maintenance / patching, optimizations, setup,
    - configuration, monitoring, failure recovery and backups
- Using ElastiCache involves heavy application code changes


## ElastiCache

Solution Architecture - DB Cache
- Applications queries
    - Amazon
ElastiCache, if not
ElastiCache
available, get from RDS
Cache hit
and store in ElastiCache.
- Helps relieve load in RDS
    - Cache miss
- Cache must have an
    - application
Read from DB
invalidation strategy to
make sure only the most
current data is used in
Write to cache Amazon
there.
RDS


## ElastiCache

Solution Architecture – User Session Store
- User logs into any of the
    - Write session
application
application
- The application writes
    - Amazon
the session data into
ElastiCache
ElastiCache
Retrieve session
- The user hits another
    - application
instance of our
application
- The instance retrieves the
    - data and the user is
application
already logged in
resU


## ElastiCache – Redis vs Memcached

REDIS MEMCACHED
- Multi AZ with Auto-Failover
- Multi-node for partitioning of
- Read Replicas to scale reads and data (sharding)
    - have high availability
- No high availability (replication)
- Data Durability using AOF
- Non persistent
    - persistence
- Backup and restore (Serverless)
- Backup and restore features
- Supports Sets and Sorted Sets • Multi-threaded architecture
    - +
Replication
sharding


## ElastiCache – Cache Security

- ElastiCache supports IAM Authentication for Redis
    - EC2 Security group
- IAM policies on ElastiCache are only used for
    - EC2 Client
AWS API-level security
- Redis AUTH
- You can set a “password/token” when you create a
    - SSL encryption
Redis cluster Redis AUTH
- This is an extra level of security for your cache (on top
    - of security groups)
Redis Security group
- Support SSL in flight encryption
- Memcached
- Supports SASL-based authentication (advanced)


## Patterns for ElastiCache

- Lazy Loading: all the read data is
    - cached, data can become stale in
Amazon
cache
ElastiCache
- Write Through: Adds or update
    - data in the cache when written
Cache hit
to a DB (no stale data)
- Session Store: store temporary
    - session data in a cache (using
TTL features)
Cache miss
application
Read from DB
- Quote: There are only two hard
    - things in Computer Science: cache
invalidation and naming things
Write to cache Amazon
RDS
Lazy Loading illustrated


## ElastiCache – Redis Use Case

- Gaming Leaderboards are computationally complex
- Redis Sorted sets guarantee both uniqueness and element ordering
- Each time a new element added, it’s ranked in real time, then added in
    - correct order
ElastiCache
1
for Redis
2
ElastiCache
3
for Redis
Clients Real-time Leaderboard
ElastiCache
for Redis
