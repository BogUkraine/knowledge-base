# AWS
This section covers the next topics:
- [Cloud](#cloud)
- [Availability zone](#availability-zone)
- [IAM](#availability-zone)
    - [IAM Policies](#policies)
    - [IAM Roles](#iam-roles)
    - [IAM Best practices](#best-practices)
- [EC2](#ec2)
    - [Configuration](#configuration)
    - [Instance Types](#instance-types)
    - [Security Groups](#security-groups)
    - [Purchasing options](#purchasing-options)
    - [EBS - Elastic Block Store](#ebs---elastic-block-store)
        - [Volume](#volume)
        - [Snapshot](#snapshot)
    - [AMI - Amazon Machine Image](#ami---amazon-machine-image)
    - [EC2 Instance Store](#ec2-instance-store)
    - [EFS - Elastic File System](#efs---elastic-file-system)
        - [EBS vs EFS](#ebs-vs-efs)
        - [EFS-IA - Infrequent Access](#efs-ia---infrequent-access)
    - [FSx - File Service](#fsx---file-service)
    - [Load Balancer](#load-balancer)
        - [Load Balancer Types](#load-balancer-types)
    - [Auto Scaling Group](#auto-scaling-group)
        - [Scaling Strategies](#scaling-strategies)
- [S3](#) - todo
- [ECS](#) - todo
- [VPC](#) - todo
- in progress

## Cloud
Cloud computing is the on-demand delivery of compute power, database storage, application, and other IT resources.
Advantages:
1. Flexibility
2. Cost-effectiveness
3. Scalability
4. Elasticity
5. High-availability and fault-torelance
6. Agility

Types of cloud computing:
* IaaS - Infrastructure as a Service - EC2
* PaaS - Platform as a Service - Elastic Beanstalk
* SaaS - Software as a Service - Rekognition
![alt text](../images/aws/cloud1.png)

Pricing:
* Compute - Lambda, EC2, ECS
* Storage - S3
* Data transfer out of the cloud

## Availability zone
Availability zone is one or more discrete data centers with redundant power, networking, and connectivity.
Each region has 3-6 availability zones.
![alt text](../images/aws/availability-zone1.png)

## IAM
IAM - Identity and Access Management - global service.

Groups only contain users, not other groups. User can belong to multiple groups and could be not a part of any group.
![alt text](../images/aws/iam1.png)

Users or Groups can be assigned with policies.

### Policies
Consists of:
* Version - policy language version - "2012-10-17"
* (optional) Id - an identifier for the policy
* Statement - one or more individual statements
```
{
    "Version": "2012-10-17",
    "Id": "Some-ID",
    "Statement": [
        {
            "Sid": "FullAccess",  - identifier
            "Effect": "Allow",    - Allow, Deny
            "Action": ["s3:*"],   - list of actions (s3:GetObject)
            "Principal": {        - account/user/role to which the policy applied to
                ...
            }
            "Resource": ["*"]     - list of resources to which the action is applied ("arn:aws:s3:::mybucket/*")
        },
        {
            "Sid": "DenyCustomerBucket",
            "Action": ["s3:*"],
            "Effect": "Deny",
            "Resource": ["arn:aws:s3:::customer", "arn:aws:s3:::customer/*" ]
        }
    ]
}
```

### IAM Roles
Secure way to grant permissions to entities in AWS.
Unlike IAM users, roles are not tied to a specific individual or service; instead, they are assumed by trusted entities such as AWS services, applications, or users.

Roles provide temporary security credentials (access keys, secret keys, and session tokens) to perform tasks based on the defined permissions. They are widely used for scenarios like granting EC2 instances access to S3, cross-account access,
or enabling AWS Lambda functions to interact with other AWS resources securely.

### Best practices
* Don't use the root account
* One physical user - one AWS user
* Assign users to groups and assign permissions to groups
* Create a strong password policy
* Use and enforce the use of MFA
* Create and use Roles for giving permissions to AWS services
* Use Access Keys for Programmatic access
* Audit permissions of your account using IAM Credentials Report & IAM Access Advisor

## EC2 - Elastic Compute Cloud
Amazon Elastic Compute Cloud (EC2) is a web service that provides secure, resizable compute capacity in the cloud. EC2’s simple interface allows to obtain and configure capacity with minimal friction.

It provides you with complete control of your computing resources and lets you run on Amazon’s proven computing environment. Amazon EC2 reduces the time required to obtain and boot new server instances to minutes, allowing you to quickly scale capacity, both up and down, as your computing requirements change. Amazon EC2 provides developers the tools to build failure resilient applications and isolate them from common failure scenarios.

### Configuration
* Operating System: Linux, Windows, Mac OS
* CPU
* RAM
* Storage-space
* Network card
* Firewall rules
* Bootstrap script

### Instance types
How to read: `m5.2xlarge`
* `m` - instance class
* `5` - generation
* `2xlarge` - size within the instance class

Types:
* General Purpose Instances: Provide a balanced mix of compute, memory, and networking resources, suitable for a variety of workloads such as web servers and code repositories. Examples include the M7g and T4g families
* Compute Optimized Instances: Designed for compute-intensive applications that benefit from high-performance processors, ideal for tasks like batch processing and high-performance web servers. The C7g family is a representative example.
* Memory Optimized Instances: Offer high memory capacity for memory-intensive applications, such as databases and real-time big data analytics. The R7g family falls into this category.
* Storage Optimized Instances: Provide high, sequential read and write access to large datasets on local storage, suitable for data warehousing and distributed file systems. Examples include the I4i and D3 families.
* Accelerated Computing Instances: Utilize hardware accelerators, or co-processors, to perform functions like floating-point number calculations and graphics processing more efficiently than software running on CPUs. The P5 and G5 families are examples.
* High-Performance Computing Instances: Purpose-built to offer the best price performance for running HPC workloads at scale on AWS, ideal for large, complex simulations and deep learning workloads. The Hpc7g family is an example.

### Security Groups
Security groups control how traffic is allowed into or out of EC2. It is a virtual firewall.
They only contain `allow` rules.

Security groups riles can be reference by IP or by security group.
![alt text](/images/aws/ec21.png)

They are locked down to a region/VPC combination.

It's good to maintain one separate security group for SSH access.

Classic ports:
* 21 - FTP
* 22 - SSH, SFTP
* 80 - HTTP
* 443 - HTTPS
* 3389 - RDP

### Purchasing options
* On-demand instances - short workload, predictable pricing, pay by second
* Reserved (1 & 3 years)
    * reserved instances - long workloads
    * convertible reserved instances - long workloads with flexible instances
* Savings Plans (1 & 3 years) - commitment to an amount of usage, long workload
* Spot instances - short workloads, cheap, can lose instances (less reliable)
* Dedicated Hosts - book an entire physical server, control instance placement
* Dedicated Instances - no other customers will share your hardware
* Capacity Reservations - reserve capacity in a specific AZ for any duration
![alt text](/images/aws/ec22.png)

### EBS - Elastic Block Store
#### Volume
EBS Volume is a network drive (not a physical drive) you can attach to your EC2. It is bound to a specific AZ.

It can only be mounted to one instance at a time, except for io1 and io2 volume types (EBS Multi-Attach feature).

#### Snapshot
Snaphot is a copy of EBS volume at a point in time. Can copy shanpshots across AZ or Region.

Features:
* Archive - to save 75%, takes 24-72 hours for restoring the archive.
* Recycle Bin for EBS Snapshots (specify retention 1 day - 1 year)

### AMI - Amazon Machine Image
AMI is a customization of an EC2 instance. This also creates EBS snapshot behind the scenes.

### EC2 Instance Store
EBS volumes are network drives with limited performance. If we need a high-performance hardware disk, use EC2 Instance Store.

* Better I/O performance.
* EC2 Instance Store lose their storage if they are stopped.
* Good for buffer/cache/scratch data/temp content.

### EFS - Elastic File System
It's managed NFS (network file system) that can be mounted on 100s of EC2. It only works with Linux EC2 instances in multi AZ.

* Highly available
* Scalable
* Expensive (3x gp2)
* Pay per use
* No capacity planning

#### EBS vs EFS
![alt text](/images/aws/ec23.png)

#### EFS-IA - Infrequent Access
That's a storage class that is cost-optimized for files not accessed every day.

* Up to 92% lower cost compared to EFS Standard
* EFS will automatically move your files to EFS-IA based on the last time they were accessed and lifecycle policy.

### FSx - File Service
Manager service to launch 3rd party high-performance file system
* Amazon FSx for Windows File Server
* Amazon FSx for Lustre (Linux): 100s GB/s, millions of IOPs, sub-ms latencies 

![alt text](/images/aws/ec24.png)

### Load Balancer
Load Balancers are servers that forward internet traffic to multiple servers (EC2) downstream.
![alt text](/images/aws/ec25.png)

#### Load Balancer Types
1. Application Load Balancer (HTTP/HTTPS/gRPC) - Layer 7
2. Network Load Balancer (high performance, allows for TCP) - Layer 4
3. Gateway Load Balancer - Layer 3
4. Classic Load Balancer (retired 2023) - Layer 4 & 7
![alt text](/images/aws/ec26.png)

### Auto Scaling Group
* scale out - add EC2
* scale in - remove EC2
* automatically register new instances to a load balancer
* replace unhealty instances

#### Scaling Strategies
* Manual Scaling
* Dynamic Scaling
    * Simple / Step Scaling - Cloud watch alarm is triggered CPU > 70% - add 2 units
    * Target Tracking Scaling - the average ASG CPU should stay at arount 40%
    * Scheduled Scaling - known patterns usage
* Predictive Scaling - uses machine learning to predict future traffic ahead of time

## S3 - Simple Storage Service
Amazon S3 is an object storage service that stores data as objects within buckets.
An object is a file and any metadata that describes the file.
A bucket is a container for objects.

* Buckets must have a globally unique name (across all regions all accounts)
* Buckets are defined at the region level
* Max object size is 5TB

Bucket types:
1. General Purpose
2. Directory

### Features
* Allows to host static content/website.
* Versioning
    * enabled at the bucket level
    * same key overwrite will change the version
    * any file that is not versioned prior to enabling versioning will have version 'null'
    * suspending versioning does not delete the previous versions
    * deleting objects with version ID null will place a delete marker and the file could be restored

### Security
* User-based
    * IAM Policies - API calls
* Resource-based
    * Bucket Policies - bucket wide rules from the S3 console
    * Object Access Control List (ACL) - finer grain (can be disabled)
    * Bucket Access Control List (ACL) - less common (can be disabled)
* Encryption - using encryption keys

### Storage Classes
* Standard: General-purpose storage for frequently accessed data.
* Intelligent-Tiering: Automatically moves data between two access tiers when access patterns change.
* Standard-IA (Infrequent Access): For data that is accessed less frequently but requires rapid access when needed.
* One Zone-IA: Lower-cost option for infrequently accessed data that does not require multiple Availability Zone resilience.
* Glacier: Low-cost storage for data archiving with retrieval times ranging from minutes to hours.
* Glacier Deep Archive: Lowest-cost storage for long-term data archiving with retrieval times of 12 hours or more.