# General information
AWS has 216 points of presence in 84 cities across 42 countrie

# Regions
How to choose a region?
It depends on the next factors
- Compliance: For example with data governance and legal requirements it could require that the data is phisically store in a specific country
- Proximity to customers: reduced latency
- Availbale services: Not all regions have all services, there is a services region table
- Pricing: The price may vary from region to region
An aws region is composite of several aws availability zones (2 to 6 zones), for example us-east (north virginia) may have us-east-1a, us-east-1b, us-east-1c and each of these availiability zones have discrete and REDUNDANT data centers, so if one of this zones is destroyed the others ones preserves the info

There are globals services that don't depend on the region and region specific services that is a fresh start on each region

# IAM
IAM stand for Identity and Access Management ans is a global service, this service allows to manage the users, groups and roles permissions
## IAM Users
User represent one person within an organization, when the account is created it was automatically created a root user, but this user shouldn't be used anymore after create the first users because it could be unsafe, we instead should create IAM users
## IAM Groups
We can create groups of users too, these groups can only contains users and not other groups.
## IAM Roles
IAM Roles are similar to users, but these are intended to be used by other aws services rather than people
Check lecture 26 - 31
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
The security groups are the fundamental of network security in AWS, these groups control how traffic is allowed into or out of EC2 instances, and can contain ONLY allow rules, finally the group rules can reference by IP or by security group.
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