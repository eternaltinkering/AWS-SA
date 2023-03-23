# AWS-SA
AWS-SA Notes
## S3 Simple Storage System
Manage data as objects rather than filesystems
- Unlimited Storage
- Objects up to 5TB in size
- Uses Buckets

- They have universal namespace, name must be unique.
- https://bucket-name.s3.region.amazonaws.com/key-name
- when uploading the browser or API will get a 200 response code
- Safe storage, always spread accoss multiple devices and facilities
- Availability 99.95 to 99.99 % depending on tier
- Durability 11 9s
- Server side encryption available
- ACLs define which accts or groups are granted access, ACL can be attached to individual objects.
- Bucket policies apply to all items in bucket
- Strong Read-After-Write Consistency, immediately available after write
        Objects Have:
        - Key: object name
        - Value: data
        - Version ID: allows versioning
        - Metadata: data about what you are storing

### Blocking Public Access
- Private by default
- ACL work on object level
- Bucket policies work on entire bucket
### S3 Hosting Static Website
- S3 Scales Automatically

### Hosting Static Content
- Use bucket policy to make content public
- Static content only (not dynamic)
- Automatic scaling on demand

### Versioning
- Old versions are not made publicly accessible
- With versioning enabled deleting doesn't actually delete
- Great backup tool
- Cannot be disables, only suspended
- Lifecycle rules
- Supports MFA

###S3 Standard Tier
- High Availability stored redundantly across multiple devices and multiple facilities (>=3 AZs)
        - 99.99% availability
        - 99.99999999999% 11 9s durability
- Designed for frequent access
- Suitable for most workloads
        - default storage class
        - websites, content distribution, mobile and gaming applications, big data analytics

### S3 Standard-IA
Designed for infrequently accessed data

### S3 Intelligent-Tiering

### Glacier
- Glacier Instant Retrieval
- Glacier Flexible Retrieval
- Glacier Deep Archive

|Storage Class|Availability and Durability|AZs|Use Case|
|-|-|-|-|
|S3 Standard|99.99% A 11 9's D|>=3|Suitable for most workloads websites, content distribution, mobile and gaming applications, big data analytics|
|S3 Standard-Infrequent Access|99.99% A 11 9's D|>=3|Long term, infrequently accessed critical data, backups, data store for disaster recovery files)|
|S3 One Zone-Infrequent Access|99.5% A 11 9's D|1|Long term, infrequently **non-critical** data|
|S3 Intelligent-Tiering|99.9% A 11 9's D|>=3|Unknown or unpredictable access patterns|
|S3 Glacier Instant Retrieval|99.99% A 11 9s D|>=3|Long-term data archiving with instant retrieval|
|S3 Flexible Retrieval|99.99% A 11 9's D|>=3|archive data that does not require immediate access but needs flexibility to retrieve large sets of data at no cost, such as backup or disaster recovery. Can be minutes or up to hours.
|S3 Glacier Deep Archive|99.99% A 11 9s D|>=3|Cheapest storage class designed for customer that retain data sets for 7-10 years or longer to meet customer needs and regulatory compliance. Standard retrieval time is 12 hours and bulk retrieval time is 48 hours.

### Lifecycle Management
Automates moving objects between storage tiers. Helps save costs.
Can be used with versioning to automatically move versioned items to save money.

### S3 Object Lock
- "WORM" model, write once read many
- Can be on objects or across buckets
- Object Lock has two modes:
        - Governance mode
        users can't overwrite or delete item unless they have special permissions
        - Compliance Mode
        **NO ONE** can alter the object including the root account

### Glacier Vault Lock
- Allows you to easily deploy and enforce compliance controls for individual S3 Glacier accts with vault lock policy. Glacier WORM
- specify controls such as WORM, in a vault lock policy and lock the policy from future edits. Once locked the policy can not be changed.

### Encryption
- Encryption in transit: SSL/TLS, HTTPS
- Encryption at rest: Server-Side Encryption
        - SSE-S3: S3-managed keys, using AES 256-bit encryption
        - SSE-KMS: AWS Key Management Service-managed keys
        - SSE-C Customer-provided keys
- Encryption at Rest: Client-Side Encryption: Files encrypted before upload
- Bucket policy can enforce encryption. Can deny all PUT requests that dont include
the `x-amz-server-side-encryption` parameter in the request header.

### Performance
#### S3 Prefixes
Buckets have a name, and then directories. Prefix is just the directory.
Does not include object name.
`bucketname/folder1/subfolder/file.txt`

- 3,500 PUT/COPY/POST/DELETE
5,500 GET/HEAD requests per second
- Spread reads across different prefixes in order to increase performance
using 2 prefixes you can get 11,000 requests per second

SSE-KMS has built in limits
- uploading/downloading will count toward the KMS quota
- Region specific, 5,500, 10,000 or 30,000 requests per second.
- Currently cannot request a quota increase for KMS

- Multi-Part Uploads
        should be used for 100MB, required for 5GB
- Multi-part downloads using byte-range to split files

### Replication
- Can replicate objects from one bucket to another
- Objects in an existing bucket are not replicated automatically
- Delete markers are not replicated by default

## EC2
Elastic Compute Cloud
- Secure
- Resizeable

### Pricing
- On-Demand: pay by the hour or second
        - Flexible: Low cost and flexible, no upfront commitment
        - Short-Term: short term spiky or unpredictable workloads, can not be interupted
        - Testing: Test new stuff

- Reserved: **Reserved capacity for 1 or 3 years**
        - Predictable usage: applications with steady state or predictable usage
        - Specific capacity requirements
        - Pay up front to reduce total cost
        - **Standard Reserved Instance up 72% off the on-demand price**
        - Convertible RI up to 54% off the on-demand price. Can change to a diffrent RI type of equal or greater value
        - Scheduled RIs: Launch
        - Region specific
- Spot:
        - **Purchase unused capacity at up to 90% off**
        - Applications with flexable start and end times.
        - Applications that are only feasible at very low compute prices
        - Urgent need of large resources
- Dedicated Hosts:
        - Compliance: regulatory requirements dont support multi-tenant
        - Licensing: may have legacy licenses
        - On-demand available
        - Reserved: up to 70% off on-demand

### CLI
- Least Privilege
- Always use groups
- Secret Access Key: only see once, needs to be regenerated if lost.
- Don't share key pairs
- Supports Windows, Mac, Linux

### Roles
- Intended to be assumed by anyone who needs access to it.
- Roles are assumed for a period of time
- Preferred option
- Avoid hard coding credentials
- Policies control a roles permissions
- Updates allows updating a policy attached to a role and will take immediate effect
- Attach and detach to running instances

### Security Groups
Virtual firewalls for EC2, by default everything is blocked.

- Changes to SG take effect immediately
- Any number of EC2 instances with a security group
- You can have multiple security groups attached to instances
- All inbound is blocked by default
- All outbound is allowed

### Bootstrap Script
- Run when the instance first runs
- Passes user data to
- Runs as root

### Metadata and Userdata
- Userdata are is bootstrap scripts
- metadata is data about your EC2 instance
- you can use bootstrap scripts to access metadata
- http://169.254.169.254/latest/meta-data/

### Networking
- ENI basic networking, low cost way to add network
- ENA Elastic Network Adapter high throughput 10Gbs and 100Gbs, reliable high throughput
- EFA Elastic Fabric Adapter: accelerate HPC and MI or OS-bypass. HPC or ML network always choose EFA.

### Placement Groups
- Cluster Placement Groups: Grouping of instances with a single AZ.** Low network latency and high network throughput.**
**can't span multiple placement AZ's, spread and placement groups can**
- Partition Placement Group: own set of racks, has its own network and power source.**Multiple EC2 instances; HDFS, HBase, and Cassandra**
- Spread: Group placed on distinct underlying hardware. **individual critical EC2 instances**
- **only certain instances can be launched in a placement group(compute optimized, GPU, memory optimized, storage optimized)**
- AWS recommends homogeneous instances within cluster placement groups
- You can't merge placement groups
- Existing instance can be moved into a placement group when stopped, only cli and SDK, not console yet.

### Licensing
Dedicated Hosts: EC2 Dedicated host is a physical server with EC2 instance capacity fully dedicated to your use. Allow you to use your exisiting per-socket, per-core, or per-VM software licenses.

### Using Spot Instances
- Stateless, fault-tolerant, or flexible applications
- Decide on maximum spot price.
- Can save up to 90% of the cost of on-demand instances
- Spot block to stop instances from being stopped for 1-6 hours
- Persistent spot requests will continually re-provision if you don't cancel it
- Sport Fleets: Try to meet capacity within price constraints, collection of spot instances and optionally regular instances.

##VMware

You can deploy vCenter on the AWS Cloud Using vMware. Order through vMware

### Outposts
AWS data center directly to you, on-premise
- Hybrid Cloud
- Fully Managed Infrastructure
- Consistency, bringing AWS into data center
- Outposts rack for large deployment in DC etc
- Outposts servers for smaller deployment like retail

## Elastic Block Storage EBS
- Designed for mission critical workloads
- Highly Available: Automatically replicated within a single AZ to protect against hardware failures
- Scalable: dynamically increase capacity and change the volume type with no downtime or performance impact

### General Purpose SSD gp2
99.9% durability

### General Purpose SSD gp3
99.9% durability

### Provisioned IOPS SSD io1
high performance
3 9's durability

### Provisioned IOPS SSD io2
Latest
5 9's durability

### Throughput Optimized HDD (st1)
Low cost HDD volume
Big data, data warehouses, ETL, and log processing
Cost-effective way of storing data mountains

### Cold HDD (sc1)
good choice for colder data requiring fewer scans per day. Good for lowest cost option.

## Volumes and Snapshots
### Snapshots
- Snapshot Exist on S3, volumes exist on EBS
- Point in time copy of a volume
- Snapshots are incremental: saves time and space
- First snapshot is going to take longer since nothing exists
- Recommended to stop the instance to ensure all data is available
- Encrypted EBS volumes are also encrypted when snapshot is stored
- Sharing: snapshots can be shared only in the region in which they were created. To share to other regions they have to be copied first.
- Will always be in the same AZ as EC2
- Can resize on the fly, file system needs to be resized in OS

## Encryption
- Data at rest is encrypted inside the volume
- all data in flight is encrypted
- all snapshots are encrypted
- all volumes created from the snapshot are encrypted

### To convert an unencrypted volume
- create snapshot of volume
- copy the snapshot and encrypt
- create an image from the snapshot
- launch an instance from the image

## Hibernation
- instance RAM is saved to the EBS and the system
- much faster to boot up
- ram must be less then 150GB
- instance families include c3,c4,c5,m3,m4,m5,r3,r4,and r5
- available for Windows, Amazon Linux 2 AMI, Ubuntu
- can't be hibernated for more then 60 days

## EFS Elastic File System
- NFSv4
- Only pay for the storage you use no-pre provisioning required
- can scale up to petabytes
- read-after-write consistency
- can support thousands of concurrent nfs connections
- data is stored across multiple AZs
- highly scalable shared storage using NFS

## FSx
### FSx for Windows
- managed windows file systemc, active directory, sharepoint. Anything native Microsoft
### FSx for Lustre Performance
- AI machine learning, high-speed, high-capacity distributed storage. High Performance Computing, financial modeling. Can store data directly on S3

## Amazon Machine Images - AMI
Provides the information required to launch an instance.
- Region
- OS
- Architecture
- Launch Permissions
- Storage for the root device

### Instance Store Volume
ephemeral storage, **cannot be stopped** if the underlying host fails, data lost. You can reboot without loosing data. Deleting instance deletes the volume.

## AWS Backup
- Consolidation use it to keep all backups organized together
- Organizations use it to backup multiple AWS services across multiple AWS accts
- Benefits: centralized control, automation and lifecyle policies. Better compliance, ensure encryption, auditing.

## Databases
- SQL Server - Microsoft
- Oracle
- MySQL
- PstreSQL
- MariaDB
- Aurora

- Multi-AZ: RDS automatically sets up an exact copy of the database inside another AZ
- Failover capability: Automatic failover, doesn't require any intervention
- Automated Capability
- Good for OLTP

### Online Transaction Processing OLTP
Process data from transactions in real time. Orders, bank transactions, payments booking.

### Multi-AZ
- exact copy of prod database in another AZ
- used for disaster recovery
- automatic failover

### Read Replica
- read only copy, of primary database in the same AZ, cross-AZ, or cross-region.
- Used to increase or scale the read performance
- Great for read-heavy workloads and takes the load off your primary database
for read-only workloads
- Requires automatic backups to be turned on

### Aurora
- 2 copies of your data are contained in each AZ with a minimum of AZ's, 6 copies of data.
- Share Aurora snapshots with AWS accounts
- 3 types of replicas: Aurora replicas, MySQL Replicas, and PostgreSQL replicas. Automated failover os only available with Aurora replicas.
- Aurora has automated backups turned on by default. You can also take snapshots with Aurora. You can share these snapshots with other AWS accounts.
- Use Aurora serverless if you want a simple, cost-effective option for infrequent, intermittent, or unpredictable workloads.

### DynamoDB
- Stored on SSD storage
- Spread Across 3 geographically distinct data centers
- Eventually consistent reads (default)
- Strongly consistent reads available
- Eventually: get consistency of data within 1 second. Best read performance
- Consistent: returns once all writes are confirmed

ACID requires enabling DynamoDB transactions

- DynamoDB transactions provide developers atomicity, consistency, isolation, and durability across 1 or more tables within a single AWS account and region.
- all-or-nothing transactions

#### DynamoDB Backups
On-demand backup and restore

- Full backups at anytime
- zero impact on table performance or availability
- Consistent within seconds
- operates within the same region as source table

- Point in time recovery
        - Protects accidental writes/deletes
        - Restore to any point in last 35 days
        - Incremental backups
        - Not enabled by default
        - Latest restorable: 5 minutes in the past

#### DynamoDB Managed Multi-Master, Multi-Region Replication
- Globally distributed applications
- Based on DynamoDB streams
- Multi-region redundancy for disaster recovery or high availability
- No application rewrites
- Replication latency under 1 second

**NOTE:** If you want to add redundancy to DynamoDB you need to turn on global tables, if global tables isn't turning on, you need to enable DynamoDB Streams.

### MongoDB
Use Amazon DocumentDB to migrate to AWS when you don't want to refactor to DynamoDB

### Cassandra
Amazon Keyspaces is fully managed Cassandra, serverless. Sometimes used as a red hearing.

### Neptune
Graph databases, often a distractor. It's just Amazons graph database.

### Amazon Quantum Ledger Database (QLDB)
Mostly a distraction. NoSQL, immutable.

### Amazon Time Series Data
Storing large amounts of time series data think Amazon Timestream


### Non-relational DB
- DynomoDB - Non-relational database, created by Amazon
### Relational DB
- MySQL, SQL Server, Aurora (MySQL compat, AWS claims it's superior)o

### Aurora
Fully compatible with MySQL and PostgreSQL. Allows existing applications to run
without modification.

## VPC

**Create a VPC the day before the exam!**
### General
Logically isolated part of AWS where you define your own network.

- Think of it as a logical data center in AWS
- Consists of Internet Gateways (or virtual private gateways) route tables, network access control lists, subnets, and security groups.
- **1 subnet is always in 1 AZ**

### Create VPC
- When you create a VPC it creates a security group, main route table, main network ACL
- AWS reserves the first three address (5 lost total because of network/broadcast address)
        - .1 VPC router
        - .2 DNS server (base of vpc range +2)
        - .3 Reserved

1. Create VPC
2. Create subnet/s
3. Optionally add internet gateway and attach to VPC
4. Add a route table for IG if needed, the IG and RT

### Using NAT Gateways
- Added to the **public** subnet
- Redundant inside the AZ
- Starts at 5Gbps scales currently to 45Gbps
- No to to patch
- Not Associated with security groups
- Automatically gets a public IP

To create a zone-independant archetecture, create a NAT gateway in each AZ and configure routing to ensure resources us the NAT gateway
in the same AZ

Basically each AZ should have its own IG, dont use just one

#### Setup
- VPC Nat Gateway: Add NAT Gateway, select public subnet, allocate EIP
- Add route to route table for all traffic 0.0.0.0/0 and assign NAT Gateway

### Security Groups
- Security Groups are stateful. Responses to inbound traffic are allowed to flow out.

### Network ACL
First line of defense.

- Default Network ACLs: VPC automatically comes with an ACL that **allows** all inbound/outbound traffic.
- Custom Network ACLs: By default **denies** all inbound/outbound traffic.
- Subnet Association: Each subnet **must** be associated with with ACL, will get assigned default if not explicitly done.
- Block IP Address: Use Network ACLs to block IPs
- Subnets can only have 1 N-ACL, assigning a N-ACL removes any previous association.
- N-ACL are ordered list of rules evaluated from lowest to highest
- Separate inbound/outbound rules
- N-ACLs are stateless

### Private Communication Using VPC endpoints
Virtual Devices, horizontally scaled, redundant and highly available VPC components
that allow communication between instances in your VPC and services without imposing
availability risks or bandwidth constraints on your network traffic.

- Connect services without leaving the Amazon internal network
- 2 Types of VPC Endpoints: Interface endpoints and gateway endpoints
- Gateway endpoints support S3 and DynamoDB

### VPC Peering

- Allows you to connect 1 VPC with another via a direct network route using private IP addresso
- Transative peering is not supported, must have a direct connection "hub and spoke model"
- You can peer between regions
- No overlapping CIDR address ranges

### Network Privacy with AWS Private Link
- Peers tens, hundreds, or thousands of customer VPCs, think AWS Private Link
- Doesn't require VPC peering; no route tables, NAT gateways, internet gateways, etc.
- Requires a Network Load Balancer on the service VPC and en ENI on the customer VPC

### Securing Network with VPN Cloudhub
- Multiple VPN sites can connect together using CLoudhub. Hub and spoke model.

### Connecting with Direct Connect
- Dedicated connection, provisioned by Amazon
- Customer/Partner connection, through a provider and they provision
- Directly connects to AWS
- Useful for high-throughput workloads
- Helpful when you need a stable and reliable secure connection

### Simplifying Networks with Transit Gateway
- You can use route tables to limit how VPCs to talk to one another
- Works with Direct Connect as well as VPN
- Supports IP multicast (no other amazon services does)

### AWS Wavelength
Mobile edge computing and 5G
