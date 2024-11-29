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