# AWS
This section covers the next topics:
- [Cloud](#cloud)
- [Availability zone](#availability-zone)
- [ ] todo

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