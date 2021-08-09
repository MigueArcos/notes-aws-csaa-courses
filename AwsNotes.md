# General information
AWS has 216 points of presence in 84 cities across 42 countrie

# Regions
How to choose a region?
It depends on the next factors
- Compliance: For example with data governance and legal requirements it could require that the data is phisically store in a specific country
- Proximity to customers: reduced latency
- Availbale services: Not all regions have all services, there is a services region table
- Pricing: The price may vary from region to region
An aws region is composite of several aws availability zones (2 to 6 zones), for example us-east (north virginia) may have ***us-east-1a***, ***us-east-1b***, ***us-east-1c*** and each of these availiability zones have discrete and REDUNDANT data centers, so if one of this zones is destroyed the others ones preserves the info

There are globals services that don't depend on the region and region specific services that is a fresh start on each region

# IAM
IAM stand for Identity and Access Management ans is a global service, this service allows to manage the users, groups and roles permissions
## IAM Users
User represent one person within an organization, when the account is created it was automatically created a root user, but this user shouldn't be used anymore after create the first users because it could be unsafe, we instead should create IAM users
## IAM Groups
We can create groups of users too, these groups can only contains users and not other groups.
## IAM Roles
IAM Roles are similar to users, but these are intended to be used by other aws services rather than people
> Check lectures 26 - 31

## IAM Recommendations and info

- Is not a good practice to have isolated users (Users that doesn't belong to any group)
- One user can belong to multiple groups
- In AWS is a good practice to apply the least privilege principle (don't give more permissions than a user needs)

## Policies
Users and groups can be asigned documents called policies which define their permissions to AWS Resources. The policies are usually attached to groups and roles, but there are policies called inline policies that are only attached to one user

## Ways to access AWS
To access AWS we have 3 options: Management Console (Web UI), CLI with access keys and secret keys and SDKs to access AWS resource from within the code using the same access and secret keys

# EC2
EC2 stands for Elastic Compute Cloud and it's the most popular Infrastructure as a Service (IaaS) offer
EC2 mainly consist in the capability of:
- Renting virtual machines (EC2)
- Storing data on virtual drives (EBS)
- Distributing load across machines (ELB)
- Scaling the services using an auto scaling group (ASG)

## Bootstrap script
In EC2 instances we can configure a bootstarp script (EC2 user data script), bootstarpping is basically launch commands when the machine starts, that script will only be launched the first time the machine is launch and then it'll never be run again. The EC2 user data is used to automate boot tasks such as: 

- Install updates
- install software
- Download common files from internet, etc, 

This scripts runs as a root user

## Instances naming example
Let's take ***m5.2xlarge*** as an example

- m = instance class (m stands for memory optimized instance)
- 5 = generation of the instance
- 2xlarge = size within the instance class

## Security groups
The security groups are the fundamental of network security in AWS, these groups control how traffic is allowed into or out of EC2 instances, and can contain ONLY allow rules, finally the group rules can reference by IP or by security group (For example allow one security group to access a shared resource like an EFS volume).
These groups are used to regulate the access to ports, authorised ip ranges (ipv4 or ipv6), inbound and outbound network traffic
> Watch lectures 44-47

## Instances IP Address
Differences between public and private IPs
- Public IP:
    - Public IP means the machine can be identified on the internet (WNW)
    - Must be unique across the whole web (not two machines can have the same public IP).
    - Can be geo-located easily
- Private IP
    - Private IP means the machine can only be identified on a private network only
    - The IP must be unique across the private network
    - BUT two different private networks (two companies) can have the same IPs.
    - Machines connect to WWW using a NAT + internet gateway (a proxy)
- Elastic IP
    - Fixed IP address that we can gitve to a specific instance
    - You own the Elastic IP as long as you don't delte it
    - You can only have 5 Elastic IPs per AWS Account (we can Ask AWS to increate that)
    - It's better to use a DNS
    - Overall, try avoiding Elastic IPs

## Placement groups (49, 50)
Sometimes you want control over the EC2 Instance placement strategy and that strategy can be defined using placement groups. When you create a placement group, you specify one of the following strategies for the group:
- Clusters: Instances into a low-latency group in a single Availability Zone
- Spreads: Instances across underlying hardware (max 7 instances per group per AZ) — critical applications
- Partition—spreads: Instances across many different partitions (which rely on different sets of racks) within an AZ. Scales to 100s of EC2 instances per group (Hadoop, Cassandra, Kafka)
> Watch lectures 49 & 50

## Elastic Network Interfaces (ENI)
Is a logical component in a VPC that represents a virtual network card
The ENI can have the following attributes:
- Primary private IPv4, one or more secondary IPv4
- One Elastic IP (IPv4) per private IPv4
- One Public IPv4
- One or more security groups
- A MAC address
- You can create ENI independently and attach them on the fly (move them) on EC2 instances for failover
- Bound to a specific availability zone (AZ)

> Watch lectures 51 & 52
> 
> For more information abount ENI check this link
> 
> https://aws.amazon.com/es/blogs/aws/new-elastic-network-interfaces-in-the-virtual-private-cloud/

## EC2 Hibernate
This fearture allows to put the RAM state in an EBS volume and then, when the instance boot up it does that much faster just like a normal hibernation in a PC, there are some limitations with feature though:
- Not all instance families are supported (Only some C, M and R)
- Instance RAM size must be less than 150Gb
- Not all Images (Operating Systems) are supported only Linux2, AMI, Ubuntu, Windows
- An instance cannot be hibernated more than 60 days

## EC2 Nitro
Is the name of the Underlying Platform for the next generation of EC2 instances, basically it consists of a New virtualization technology with the following benefits:

- Allows for better performance:
- Better networking options (enhanced networking, HPC, IPv6)
- Higher Speed EBS (Nitro is necessary 4,000 EBS IOPS — max 32,000 on non-Nitro)
- Better underlying security

> Note: Not all instances are supported yet, but you are always encouraged to use the instances that already support this feature.

## EC2 vCPU
A vCPU (virtual CPU) is a thread from the real processor

## EC2 Capacity reservation
Capacity Reservations ensure you have EC2 Capacity when needed, this can be combined with Reserved Instances and Savings Plans to do cost saving, the advantages of this feature are:

- Manual or planned end-date for the reservation
- No need for 1 or 3-year commitment
- Capacity access is immediate, you get billed as soon
as It starts

To use use the capacity reservation we must specify the following:

- The Availability Zone in which to reserve the capacity (only one)
- The number of instances for which to reserve capacity
- The instance attributes, including the instance type, tenancy, and platform/OS

> Watch lecture 56 for more information

## EC2 Instance Storage
The EC2 instances can have different storage options, this options are the following: 
### EBS Volumes
An EBS (Elastic Block Store) Volume is a network drive you can attach to your instances while they run. These volumes allow your instances to persist data, even after their termination, the EBS volumes have the following features:

- They can only be mounted to one instance at a time (at the CCP level)
- They are bound to a specific availability zone
- Analogy: Think of them as a "network USB stick"
- You can increase capacity over time
- EBS volumes are locked to an specific availability zone (***us-east-1a*** as an example), however you can snapshot an EBS to move it to another availability zone
- When an EC2 instance is terminated by default the EBS volume attached to it is deleted, but this can be changed and move the volume to another instance

With the free tier you have access to free EBS storage of 30Gb
> Watch lecture 57 & 58 for more information

One powerful feature of EBS volumes is that you can make backups, this feature is called EBS snapshots.
This way you can copy the storage content of one instance at a point in time and then use this snapshot to restore again to another EBS or to move it to other availability zones or even other regions since the EBS Snapshots are linked to one AWS Region.

EBS Volumes comes in 6 types:

- gp2/gp3: General purpose SSD
- io1/io2: Highest perfomance SSD (For DBs for example)
- st1: Low cost HDD for frequent access
- sc1: Lowest cost HDD for data infrequently accessed

For EC2 instances only SSDs can be used as boot volumes
> Watch lecture 63 for more information about the differences between the volume types

#### EBS Volumes RAID Settings
The EBS volumes can be configured as RAID to increase performance or fault tolerancy, 
To mount a RAID in one EC2 instance we must create a logical volume that contains the n EBS volumes attached to it

AWS supports the following RAID modes:

- RAID 0: To increase performance by combining 1 or more volumes disk spaces and IOPS (This increases fault risk because if only one EBS volume fails all the data is lost)
- RAID 1: To increase fault tolerancy using mirroring
- RAID 5: Not recommended
- RAID 6: Not recommended
- RAID 10: Not recommended

#### EBS Multi-Attach
There is also another feature called EBS multi attach, this way you can attach an EBS volume (Only of the family io1 or io2) to multiple EC2 instances (up to 3 instnaces in the same availability zones), and each instances has full RW access on the EBS, Also the EBS volume needs to use a cluster-aware file system like XFS, EX4, etc. It's not a very common setup but it allows to get a better perdfomance, but the app running there must manage concurrent write operations.

### AMI (Amazon Machine Images)
AMI are a customization of an EC2 instance
You add your own software, configuration, operating system, monitoring ... etc, the advantage of AMIs are the following:

- Faster boot / configuration time because all your software is pre-packaged
- AMI are built for a specific region (and can be copied across regions)
- You can launch EC2 instances from:
    - A Public AMI: AWS provided
    - Your own AMI: you make and maintain them yourself
    - An AWS Marketplace AMI: an AMI someone else made (and potentially sells)

The process to create an AMI is the following:

- Start an EC2 instance and cutomize it
- Stop the instance (For data integrity)
- Build an AMI from the instance (This will create an EBS behind the scenes)
- Launch instances from the nely created AMI 

### EC2 Instance Store
These are a high performance disks connected phisically to an EC2 instance, they are faster than the EBS volumes but the caveat is that when the instnaces are terminated the data is lost (ephemeral storage), so there is a risk of data lost if hardware fails, but this stoareg option can be a great choice for buffer, cache or temporary content

### EFS (Elastic File System)
Is a Managed NFS (Network File System) that can be mounted on many EC2 instances, EFS works with EC2 instances in multi-AZ (same region).
EFS is Highly available, scalable, expensive (3x gp2), pay per use. 
EFS uses NFSv4.1 protocol and only works with Linux based AMIs (Only works for POSIX file systems), uses a security to control the access to it.
The most important features about EFS are the following:

- EFS Scale
    - 1000s of concurrent NFS clients, 10 GB+ /s throughput
    - Grow to Petabyte-scale network file system, automatically
- Performance mode (set at EFS creation time)
    - General purpose (default): latency-sensitive use cases (web server, CMS, etc... )
    - Max I/O — higher latency, throughput, highly parallel (big data, media processing)
- Throughput mode
    - Bursting (l TB = 50MiB/s + burst of up to 100MiB/s) (The bursting increases as the storage increases)
    - Provisioned: set your throughput regardless of storage size, ex: I GiB/s for I TB storage (deterministic way to set the throughtput and the size independtly)
- Storage Tiers (lifecycle management feature — move file after N days and change the price for infrequently accesed files (EFS-IA))
    - Standard: for frequently accessed files
    - Infrequent access (EFS-IA): cost to retrieve files, lower price to store

> Watch lecture 68

## High availability and scalability
The applications running in AWS can scale as long as the business grows, there are 2 types of scalability in AWS.

- Vertical Scaling: Increase the size and performance of an instance (go from t2.micro to t2.2xlarge as exmaple) when the application inside it is running out of resource, good use case can be RDS, ElasticCache among others
- Horizontal Scaling: Increase the number of instances in a distributed system, good use cases could be a web application or service, this can be achieved by using an Auto Scaling Group or a Elastic Load Balancer. (When the number of instance increase it is a scale out and when the number decrease then it is a scale in)

High availability is realted to scaling but is is not exactly the same, in this context High Availability meand that you are running instances for the same application in different availability ones so if zone does not work the other one operates normally, this can be achieved by using an Auto Scaling Group or a Elastic Load Balancer with multi AZ enabled

## Load balancing
Load balancers are servers that forward internet traffic to muliple EC2 instances downstream. The load balancer also checks if the instance is ready to respond a client by running health checks every few seconds (Configurable time).
There are 3 kinds of managed load balancers: 

- Classic load balancer (v1 - old generation) launched in 2009: Allows HTTP, HTTPS and TCP
- Application Load Balancer (v2 - new generation) launched in 2016: Allows HTTP, HTTPS and WebSocket
- Network Load Balancer (v2 - new generation) launched in 2017: Allows TCP, TLS (Secure TCP) and UDP
- Gateway Load Balancer: See lecture 74

It's recommended to use the newer load balancers (v2) as they provide more features.
Load balancers can be public maning that every user can access it throught is IP or or its DNS name, or private so it is accesible only within the AWS account

### Application Load Balancing (ALB)
Application load balancers is Layer 7 (HTTP), its main features are the following:

- Load balancing to multiple HITP applications across machines (target groups)
- Load balancing to multiple applications on the same machine (ex: containers)
- Support for HTTP/2 and WebSocket
- Support redirects (from HTTP to HTTPS for example)
- Support routing based on path, on hostname, on query string and on headers
- Is more power than a classic CLB because this supports multiple applications whereas the CLB only supports one application per CLB

What is a target group? Normally it is an EC2 instnace, a ECS task, a Lambda function or an IP Address (This can be seen as a microservice) and the ALB is forwarding the traffic to the corresponding ms based for exmaple on the path /users = users ms, /tasks = task ms

### Network Load Balancer 
These kind of load balancers sits on the layer 4, the main features of NLB are the following:

- NLB are the most powerful ones since they are able to hanlde millions of requests per second, 
- NLB have the less latency (~100ms vs 400ms for ALB)
- NLB **ARE NOT INCLUDED** in the AWS Free Tier
- NLB are used for extreme performance, TCP or UDP traffic
- NLB has one static IP per AZ and supports assigning elastic IPs

### Sticky sessions
It is possible to implement stickiness so that the same client is always redirected to the same instance by the load balancer, this is done usually using cookies (Application-based cookies generated by the applicatuion itself and duration-based cookies generated by the load balancer). One use case of this stickiness is to make sure that the user doesn't lose its session. 
The disadvantage of this approach is that maybe bring imbalance to the load over the EC2 instances in case some instances have sticky users.

> Watch lecture 81 for more information

### Cross zone load balancing
This feature allows to distribute the traffic evenly between all instances registered in multiple load balancers across mutiple availability zones. 
For example, if you have a load balancer for ***us-east-1a*** with 2 instances and another one on ***us-east-1b*** with 8 instances, you have 10 instances in total, so the traffic goes 50% in each load balancer but only 10% per instance, on the other hand with cross zone disabled each instance in the load balancer with 2 instances would receive 25% of the traffic and each instance in the load balancer with 8 instances would receive 6.25% of the traffic.
Enabling or disabling this feature depends of the specific requirements of the app, so there is not a right or wrong answer about when to use it.
The following is information about this feature in the different types of load balancers: 

- Application load balancer
    - Always on (can't be disabled)
    - No charges for inter AZ data
- Network Load Balancer
    - Disabled by default
    - You pay charges ($) for inter AZ data if enabled
- Classic Load Balancer
    - Through Console => Enabled by default
    - Through CLI / API => Disabled by default
    - No charges for inter AZ data if enabled

### SSL certificates and load balancers
An SSL Certificate allows traffic between your clients and your load balancer to be encrypted in transit (in-flight encryption).
SSL refers to Secure Sockets Layer, used to encrypt connections, while TLS refers to Transport Layer Security, which is a newer version Of SSL.

- Nowadays, TLS certificates are mainly used, but people still refer as SSL
- Public SSL certificates are issued by Certificate Authorities (CA) (Ex. Comodo, Symantec, GoDaddy, GlobalSign, Digicert, Letsencrypt, etc...)
- SSL certificates have an expiration date (you set) and must be renewed

How SSL certificates works with load balancers?
- The load balancer uses an X.509 certificate (SSL/TLS server certificate)
- You can manage certificates using ACM (AWS Certificate Manager)
- You can create upload your own certificates alternatively
- For HTTPS Listener:
    - You must specify a default certificate
    - You can add an optional list of certs to support multiple domains
    - Clients can use SNI (Server Name Indication) to specify the hostname they reach
    - Ability to specify a security policy to support older versions of SSL / TLS (legacy clients)

The folowing is a comparison between the SSL certificates for the different load balancers:

- Classic Load Balancer (V1)
    - Support only one SSL certificate (Because the cLB only supports one app)
    - Must use multiple CLB for multiple hostname with multiple SSL certificates
- Application Load Balancer (v2)
    - Supports multiple listeners with multiple SSL certificates
    - Uses Server Name Indication (SNI) to make it work
- Network Load Balancer (v2)
    - Supports multiple listeners with multiple SSL certificates
    - Uses Server Name Indication (SNI) to make it work

#### SNI (Server Name Indication)
Using SNI or Server Name Indication, you are able to have multiple target groups for different websites using different SSL certificates. This is only supported in ALB or NLB (newer generation load balancers), doesn't work for CLB. SNI is a newer protocool and requires the client to indicate the hostname of the target server in the initial SSL handshake, then the server will find the correct certificate or return a default one.

### ELB Connection Draining
This feature has different names, for CLB it's actually Connection Draing, but for ALB & NLB (for target groups) the na.me is Deregistration Delay.
The definition could be the time to complete in-flight-request while the instance is deregistering or unhealthy, with this feature enabled the LB stops sending new requests to the instance which is deregistering, it can be a value between 1 to 3600 seconds, and also can be disabled by setting the value to 0.

### ASG (Auto Scaling Group)
The goal of an Auto Scaling Group (ASG) is to:

- Scale out (add EC2 instances) to match an increased load
- Scale in (remove EC2 instances) to match a decreased load
- Ensure we have a minimum and a maximum number of machines running
- Automatically Register new instances to a load balancer

An ASG have the following attributes: 

- A launch configuration
    - AMI + Instance Type
    - EC2 User Data
    - EBS Volumes
    - Security Groups
    - SSH Key Pair
    - Min Size / Max Size / Initial Capacity
- Network + Subnets Information
- Load Balancer Information
- Scaling Policies
    - Dynamic scaling policies (Scale based on metrics such as Average CPU, schedule, inbound traffic, outbound traffic, num. of request per second, etc, or custom metrics)
    - Predictive scaling policies (Uses machine learning to know when to scale the number of instances based on forecast by previous events.)
    - Scheduled actions (No need to specify, :v)

Good metrics to scale the number of instances:

- CPU Usage
- Request count per target (Ex. 1000 rps per instance)
- Average network (in/out) (Mostly for apps that need high bandwidth like video or audio processing)
- Any custom metric (uysing CloudWatch)

#### Scaling cooldown
Is a period (default 5 minutes but it can be configured) in which a scale activity happens, so during that period the ASG won't launch or terminate additional instances (To allow metrics to stabilize), because of this period is good to use ready-to-use AMI to reduce configuration time in order to be serving requests faster and reduce the cooldown period

> Watch lecture 88
#### ASG Alarms
It is possible to scale an ASG based on CloudWatch alarms. An Alarm monitors a metric (such as Average CPU, schedule, inbound traffic, outbound traffic, num. of request per second, etc, or custom metrics), then these metrics are computed for the overall ASG instances.
Based on the alarm ee can create scale-out policies (increase the number of instances) and scale-in policies (decrease the number of instances)

#### ASG default termination policy
The Default Termination Policy for ASG tries to balance across AZ first (if 3 in ***us-east-1a*** and 4 in ***us-east-1b***, first it will terminate instance(s) in the b AZ), and then delete based on the age of the launch configuration.

> Watch lecture 89

#### ASG Lifecycle Hooks
By default as soon as an instance is launched in an ASG it's in service.

- You have the ability to perform extra steps before the instance goes in service (Pending state)
- You have the ability to perform some actions before the instance is terminated (Terminating state)

### Launch templates vs launch configurations 

- Both:
    - ID of the Amazon Machine Image (AMI), the instance type, a key pair, security groups, and the other parameters that you use to launch EC2 Instances (tags, EC2 user-data...)
- Launch Configuration (legacy):
    - Must be re-created every time
- Launch Template (newer):
    - Can have multiple versions
    - Create parameters subsets (partial configuration for re-use and inheritance)
    - Provision using both On-Demand and Spot instances (or a mix)
    - Can use T2 unlimited burst feature
    - Recommended by AWS going forward

# RDS (Relational Database Service)
It's a managed DB service for DB, it uses SQL as a query language and allows  to create databases in the cloud that are managed by AWS.
The following are the Databases supported by RDS:

- Postgres
- MySQL
- MariaDB
- Oracle
- Microsoft SQL Server
- Aurora (AWS Proprietary database)

The features of RDS are the following: 

- Automated provisioning, OS patching
- Continuous backups and restore to specific timestamp (Point in Time Restore)!
- Monitoring dashboards
- Read replicas for improved read performance
- Multi AZ setup for DR (Disaster Recovery)
- Maintenance windows for upgrades
- Scaling capability (vertical and horizontal)
- Storage backed by EBS (gp2 or io l)
- **YOU CAN'T**  SSH into your instances

## RDS Backups
Backups are automatically enabled in RDS

- Automated backups:
    - Daily full backup of the database (during the maintenance window)
    - Transaction logs are backed-up by RDS every 5 minutes
    - Ability to restore to any point in time (from oldest backup to 5 minutes ago)
    - 7 days retention (can be increased to 35 days)
- DB Snapshots:
    - Manually triggered by the user
    - Retention of backup for as long as you want

## RDS Auto scaling
When RDS detects you are running out of free database storage, it scales automatically. This avoids manually scaling the database storage, to do so
 you have to set Maximum Storage Threshold (maximum limit for DB storage)-
This is useful for applications with unpredictable workloads and supports all RDS database engines (MariaDB, MySQL, PostgreSQL, SQL Server, Oracle)

## RDS Read Replica
A read replica is a copy of a RDS instance, that is used only for lectures (SELECT), the synchronization happens async, so read are eventually consistent.
One main RDS instance can have up to 5 read replicas within AZ, Cross AZ or Cross Region, to use the read replicas the applications connecting to RDS have to update their connection strings to connect to the replicas.
One use case for the read replicas is the following:

- You have a production database that is taking on normal load
- You want to run a reporting application to run some analytics
- You create a Read Replica to rur the new workload there
- The production application is unaffected

> Note: In AWS there's a network cost when data goes from one AZ to another, but since RDS is a managed service you don't have to pay that fee for Read Replicas within the same region (But you would need to pay in case the read replica is in another region)

## RDS Multi AZ
RDS Multi AZ have the following features:

- SYNC replication
- One DNS name — automatic app
- failover to standby
- Increase availability
- Failover in case of loss of AZ, loss of network, instance or storage failure
- No manual intervention in apps
- Not used for scaling

> Note: The Read Replicas can be setup as Multi AZ for Disaster Recovery (DR)

## RDS Security Summary
We have 2 Type of encryptions: In Flight Encryption and At rest Encryption
- For Encryption at rest:
    - Is done only when you first create the DB instance
    - Or: unencrypted DB => snapshot => copy snapshot as encrypted => create DB from snapshot
- For In Flight Encryption
    - Use SSL Certificates to encrypt data in flight
    - The SSL option must be enabled in the database engine
- Your responsibility:
    - Check the ports / IP / security group inbound rules in DB's SG
    - In-database user creation and permissions or manage through IAM (IAM only works for mysql and postgresql)
    - Creating a database with or without public access
    - Ensure parameter groups or DB is configured to only allow SSL connections
- AWS responsibility:
    - No SSH access
    - No manual DB patching
    - No manual OS patching
    - No way to audit the underlying instance

> Note: If a master DB is not encrypted the read replicas cannot be encrypted

## RDS Aurora 
Aurora is a proprietary technology from AWS (not open sourced). Postgres and MySQL are both supported as Aurora DB (that means your drivers will work as if Aurora was a Postgres or MySQL database).
Aurora is "AWS cloud optimized" and claims 5x performance improvement over MySQL on RDS, over 3x the performance of Postgres on RDS.
The main features of Aurora are the following:

- Aurora storage automatically grows in increments of I OGB, up to 64 T B.
- Aurora can have 15 replicas while MySQL has 5, and the replication process is faster (sub 10 ms replica lag)
- Failover in Aurora is instantaneous. It's HA native.
- Aurora costs more than RDS (20% more) — but is more efficient
- It has a DNS Writer endpoint, this endpoint will automatically route the data to the master instances (also named writer instances)
- It has a reader endpoint, this endpoint will automatically read data from any of the replica instances (This happen under the hoods)
- It uses a shared storage volume for the cluster that can grow automatically from 10GB up to 64TB
- It's the same than a RDS of MySQl or Postgres in terms of security (IAM capable, encryption, automated backups)
- There are 4 modes, One Writer & Multiple Reads (General purpose, the volume is splitted in 100s around different availability zones, we can enable Multi AZ)One Writer & Multiple Reads - Parallel Query, Multiple Writers,  Serverless (Unpredictable workload, based on the load it will scale the number of replicas and the storage capacity)
- We can setup auto sacling for aurora replicas
- We can create custom endpoints, for example create powerful instances in the cluster for big data quries, accessthese instances throught that custom endpoint and access the less powerful instances throught another custom endpoint, generally when using custom endpoints we don't use the reader endpoint itself, but it still works
- Aurora Multi-Master is a feature that allows every node in the cluster to be RW, instead of only one (or more) writer instance(s), this way we have inmediate faiolver in case the writer instance(s) fails because it doesn't have to convert one of the replicas to the new writer instance
- Global Aurora can be setup using Cross Regional Read Replicas or Aurora Global Database (Recommended), this way there is one primary region where RW happens, up to 5 secondary regions (read only, lag < 1 second) and up to 16 replicas per secondary region. In case of disaster in the main region it takes less than one minute to promote to another region the data
- Aurora has integrations with AWS Machine Learning services such as SageMaker, Comprehended, use case of this are for example antifraud, ads targetting or product recommendations

# ElastiCache
ElastiCache is to get managed Redis or Memcached, the same way RDS is to get managed Relational Databases. Caches are in-memory databases with really high performance and low latency.
Features of ElastiCache:

- Helps reduce load off of databases for read intensive workloads
- Helps make your application stateless
- AWS takes care of OS maintenance / patching, optimizations, setup, configuration, monitoring, failure recovery and backups
- Using ElastiCache involves heavy application code changes
- Cache must have and invalidation strategy to make sure the newest data is used in there
- Use case: Store session data in ElastiCcahe to avoid losing that data for example in a Load Balancer
- ElastiCache caches (redis & memcached) don't support IAM Authentication, IAM is only at Elasticache service level
- Redis supports password/token auth and SSL encryption
- Memcached supports SASL based authentication
- There are 3 patterns for ElastiCache
    1. Lazy Loading: all the read data is cached, data can become stale in cache
    2. Write Through: Adds or update data in the cache when written to a DB (no stale data)
    3. Session Store: store temporary session data in a cache (using TTL features)
- Redis have one nice and super useful feature, the **Sorted Sets**, this sets allows us to create for example a leaderboard in real time, because it gurantees uniqueness and element ordering. Each time a new element is added it's ranked in real time and added in correct order

> Quote: *There are only two hard things in Computer Science: cache invalidation and naming things*

# Route53
Route53 is a Managed DNS (Domain Name System)
In AWS, the most common records are:

- A: hostname to IPv4
- AAAA: hostname to IPv6
- CNAME: hostname to hostname (Only works for non root domain ex. app.mydomain.com, any host allowed)
- Alias: hostname to AWS resource (can be either a root or non root domain)

Route53 can use:
- Public domain names you own (or buy)
- Private domain names that can be resolved by your instances in yourVPCs.

Route53 has advanced features such as:

- Load balancing (through DNS — also called client load balancing)
- Health checks (although limited... )
- Routing policy: simple, failover; geolocation, latency, weighted, multi value

Route53 cost $0.50 per month per hosted zone

> The DNS protocol does not allow you to create a CNAME record for the top node of a DNS namespace (mycoolcompany.com), also known as the zone apex

## Route53 TTL
Each time we create a record in Route53 we must specify the TTL (Time To Live) for that record, the TTL is the time that client machines are going to cache the DNS name to avoid go again and again for the address of that domain name

## Route53 Simple Routing Policy
Used when need to redirect to a single resource, you can't attach health checks to simple policies. 
If multiple values are returned as the resolution of the DNS then a random one is chosen by the **CLIENT**, this is called client side load balancing

## Route53 Weighted Policy
This policy controls the % of the requests that go to specific endpoint. It could be helpful to test % of traffc on new app version for example and also is helpful to split traffic between two regions. It can be associated with health Checks, to use it you need to create multiple records of type Weighted with the same DNS name and set an specific weight to each record, can be a value between 0-255 but in the end it will take an average to calculate the % of traffic

## Route53 Latency Policy
This policy redirects to the server that has the least latency close to us.
It's super helpful when latency of users is a priority. Latency is evaluated in terms of user to designated AWS Region (Ex. Germany may be directed to the US (if that's the lowest latency))

## Route53 Health Checks
We can enable health checks for Route53, those health checks works on HTTP, HTTPS (No SSL verification) and TCP. You can configure 2 intervals for health health checks: 30s (Default) and 10s (mosre expensive)

## Route53 Failover Policy
This policy redirects a user to one instance (called primary) in all cases but if that instance is unhealthy (You must enable health checks on primary) then the traffic is send to the secondary instance (This does not need to have health checks attached to it)

## Route53 Geolocation Policy
This is routing based on user location, you define rules per country or per continent (Mx connect to this intance, Ireland to this other one and South America to this other one)

## Route53 Geoproximity Policy
Route traffc to your resources based on the geographic location of users and resources
Ability to shift more traffc to resources based on the defined bias
To change the size of the geographic region, specify bias values:
- To expand (l to 99) — more traffic to the resource
- To shrink (-1 to -99) — less traffic to the resource

## Route53 Multivalue Routing Policy

- Use when routing traffic to multiple resources
- Want to associate a Route 53 health checks with records
- Up to 8 healthy records are returned for each Multi Value query
- Multi Value is not a substitute for having an ELB

## Route53 Third Party
A domain name registrar is an organization that manages the reservation of Internet domain names
Famous names:

- GoDaddy
- Google Domains
- Etc...
- And also... Route53 (e.g. AWS)!

Domain Registrar != DNS

If you buy your domain on 3rd party website, you can still use Route53.

1. Create a Hosted Zone in Route 53
2. Update NS (Name servers) Records on 3rd party website to use Route 53 name servers

# 5 Pillars for a well architected apllication

- Cost
- Performance
- Reliability
- Security
- Operational Excellence

# Elastic Beanstalk
Elastic Beanstalk is a developer centric view of deploying an application on AWS.
It uses all the component's we've seen before: EC2, ASG, ELB, RDS, etc and it's a managed service with the following features:

- Automatically handles capacity provisioning, load balancing, scaling, application health monitoring, instance configuration, etc
- Just the application code is the responsibility of the developer
- We still have full control over the configuration
- Beanstalk is free but you pay for the underlying instances

Elastic Beanstalk comes in game to solve the following problems that lots of developers have in their daily basis:

- Managing infrastructure
- Deploying Code
- Configuring all the databases, load balancers, etc
- Scaling concerns
- Most web apps have the same architecture (ALB + ASG)
- All the developers want is for their code to run!
- Possibly, consistently across different applications and environments

## Elastic Beanstalk components
- Application: collection of Elastic Beanstalk components (environments, versions, configurations, etc)
- Application Version: an iteration of your application code
- Environment
    - Collection of AWS resources running an application version (only one application version at a time)
    - Tiers: Web Server Environment Tier & Worker Environment Tier
    - You can create multiple environments (dev, test, prod, etc)

## Elastic Beanstalk supported platforms
Elastic Beanstalk suports the following programming languages: Go, Java SE, Java with Tomcat, .NET Core on Linux, .NET on Windows Server, Node.js, Python, Ruby, Packer Builder, Single Container Docker, Multi-container Docker, Preconfigured Docker
And if not supported, you can write your custom platform (advanced)

# S3
Amazon S3 allows people to store objects (files) in "buckets" (directories)
## S3 Buckets
Buckets must have a globally unique name across all accounts in AWS, they are defined at the region level.
Naming convention for Buckets:

- No uppercase
- No underscore
- 3-63 characters long
- Not an IP
- Must start with lowercase letter or number

## S3 Objects
Object values are the content of the body, the max Object Size is 5TB (5000GB) but if you are uploading more than 5GB, must use "multi-part upload"
Also, objects have the following properties:

- Metadata (list of text key / value pairs — system or user metadata)
- Tags (Unicode key / value pair — up to 10) — useful for security / lifecycle
- Version ID (if versioning is enabled)

## S3 Versioning
You can version your files in Amazon S3. Versioning is enabled at the bucket level.
Same key overwrite will increment the "version": l, 2, 3, etc. It is best practice to version your buckets always because it helps you with the folowing concerns: 

- Protect against unintended deletes (ability to restore a version)
- Easy roll back to previous version

> Notes:
>
> Any file that is not versioned prior to enabling versioning will have version "null"
>
> Suspending versioning does not delete the previous versions

## S3 Encryption
There are 4 methods of encrypting objects in S3

- SSE-S3: encrypts S3 objects using keys handled & managed by AWS (AES-256)
    - Must set header "x-amz-server-side-encryption":"AES256" when uploading
    - Downside: The data key is entirely owned by AWS 
- SSE-KMS: leverage AWS Key Management Service to manage encryption keys (Customer Master Key (CMK))
    - Must set header "x-amz-server-side-encryption":"aws:kms" when uploading
    - Advantages: User control and audit trail
- SSE-C: when you want to manage your own encryption keys
    - S3 does not store the encryption keys you provide
    - Must use HTTPS because you're sending a secret to AWS
    - Encryption keys must be provided in HTTP Headers, for every HTTP request made
    - When retrieving an object you must provide the same data key that was used to encrypt it in the headers
- Client Side Encryption (CSE)
    - Encrypt the object before sending it to AWS using libraries
    - Clients must encrypt and decrypt data themselves when reading or creating data
    - Client manages entirely the keys and the encryption cycle

> SSE stands for Server Side Encryption

Amazon S3 exposes 2 endpoints, one HTTP (non encrypted) and another one HTTPS (Encryption in flight). You can use the endpoint you want but HTTPS is recommended, most clients included the aws console use the HTTPS endpoint by default, in the case of SSE-C HTTPS endpoint is mandatory, this encryption in flight is also called SSL/TLS. When using HTTPS data in transit between the client and S3 is going to be fully encrypted.

## S3 Security & Bucket Policies
There are 2 types of Security on S3
- User based
    - IAM policies - which API calls should be allowed for a specific user from IAM console
- Resource Based
    - Bucket Policies - bucket wide rules from the S3 console - allows cross account
    - Object Access Control List (ACL) — finer grain
    - Bucket Access Control List (ACL) — less common

> Note: an IAM principal can access an S3 object if the user IAM permissions allow it OR the resource policy ALLOWS it AND there's no explicit DENY (If there is adeny in any part then the user cannot access the object)

The Bucket Policies are Json based policies, and they are composite of the following elements:

- Resources: buckets and objects
- Actions: Set ofAPl to Allow or Deny
- Effect: Allow / Deny
- Principal: The account or user to apply the policy to

The Bucket policies are used to:

- Grant public access to the bucket
- Force objects to be encrypted at upload
- Grant access to another account (Cross Account)

### Block S3 public access
Block public access to buckets and objects granted through

- new access control lists (ACLs)
- any access control lists (ACLs)
- new public bucket or access point policies

Block public and cross-account access to buckets and objects through any public bucket or access point policies

> These settings were created to prevent company data leaks. If you know your bucket should never be public, leave these on.
> 
> Can be set at the account level

### Other S3 security features

- Networking:
    - Supports VPC Endpoints (for instances in VPC without internet)
- Logging and Audit:
    - S3 Access Logs can be stored in other S3 bucket
    - API calls can be logged in AWS CloudTrail
- User Security:
    - MFA Delete: MFA (multi factor authentication) can be required in versioned buckets to delete objects
- Pre-Signed URLs: URLs that are valid only for a limited time (ex: premium video service for logged in users)

## S3 Websites
S3 can host static websites and have them accessible on the www

The website URL will be:

- <bucket-name>.s3-website-<AWS-region>.amazonaws.com
- OR
- <bucket-name>.s3-website.<AWS-region>.amazonaws.com

If you get a 403 (Forbidden) error, make sure the bucket policy allows public reads!

## S3 Cors
> A different origin is even one subdomain
If a client does a cross-origin request on our S3 bucket, we need to enable the correct CORS headers
> It's a popular exam question
You can allow for a specific origin or for * (all origins)

