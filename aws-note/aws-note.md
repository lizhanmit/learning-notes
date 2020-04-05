# AWS Note

## Overview

Four primary benefits of using cloud services:

- high availability 
- fault tolerant
- scalability
- elasticity

![aws-architecture.png](img/aws-architecture.png)

![aws-architecture-2.png](img/aws-architecture-2.png)

---

## Global Infrastructure

**AWS region**: 

- Each region is a geographical area, which is a collection of AWS availability zones and data centers. 

Define different services in the same region if you want them to interact.

**Availability zone**: 

- A geographical physical location that holds in AWS **data center**. 
- Each availability zone is geographically separated from the other. 
- Multiple availability zones are for redundancy. 

---

## DynamoDB

A NoSQL database service for all applications that need consistent, single-digit millisecond latency at any scale. 

The **primary key** is made up of a partition key (hash key) and an optional sort key. 

The **partition key** is used to partition data across hosts for scalability and availability. Choose an attribute which has a wide range of values and is likely to have evenly distributed access patterns.

---

## EC2

Elastic Compute Cloud (EC2): It is the virtual equivalent of the server computer.

Common uses: 

- As a web hosting server.
- Be good for any type of "processing" activity such as encoding and transcoding.

---

## IAM

**Good practice**: bond groups and policies, then add users to groups. 

- users
- groups
- policies
- rules - services

Think of the **user pool** as a database for user accounts that allows them to login and the **identity pool** gets them into the right role to give them permissions into the rest of AWS.


### Cognito 

It offers: 

- User pools: provides sign-up and sign-in options for app users.
- Identity pools: provides AWS credentials to users to access AWS services.

---

## Lambda

serverless

---

## RDS 

Relational Database Service (RDS):  AWS provisioned database service.

Common uses: 

- Storing customer account information and cataloging inventory.

---

## S3

Simple Storage Service (S3): Basically just a large "unlimited" storage bucket.

- Provides object storage.
- Multiple redundancies and backups of the files.

Common uses: 

- Mass storage. 
- Long term storage.

![s3.png](img/s3.png)

To upload a file larger than 160 GB, use the AWS CLI, AWS SDK, or Amazon S3 REST API.

You can access S3 buckets across regions. But if you are defining something like a lambda that is going to use this bucket, you might want to define it in the same region for performance. 

You would not want to store sensitive data in a region of another country. 

---

## SNS

Simple Notification Service (SNS)

- topics
- subscribers

Whenever there is a message for SNS on a topic, it pushes it out to all of its subscribers.

SQS can be a subscriber.

You can create notification using S3, e.g. the event that a new file is PUT. Then the message flow: S3 -> SNS -> SQS -> Lambda function that processes the message.



---

## SQS

Simple Queue Service (SQS): a reliable, scalable, fully-managed message queuing service.

- Send, store, and receive messages between apps and software components. 
- Decouple and scale better.
- User server side encryption (SSE) and Key Management Service (KMS) for security.

Two types of queues: 

- Standard: maximum throughput, best-effort ordering (not guaranteed the order), at-least-once delivery
- FIFO: processed exactly once, in order

Example: A Lambda function that can process messages. Configure trigger for the Lambda function on queue when the message is ready and send the message via message body and its attributes. And you can watch that through CloudWatch Logs.

---

## VPC

Virtual Private Cloud (VPC):  Your private section of AWS, where you can place AWS resources, and allow / restrict access to them.

![vpc.png](img/vpc.png)

---

## Create an Elastic Web Application by Using AWS

![elastic-web-app-deployment-architecture.png](img/elastic-web-app-deployment-architecture.png)

公网对外，私网对内。 私网不能直接访问公网，需通过NAT（相当于router）。

---

## Glossary 

- ARN: AWS Resource Name
- IdP: Identity Provides and Federation 
- STS: Security Token Service
- WIF: Web Identity Federation 

---

## Questions Collection 

Q: In IAM, which areas need to be considered with restriction and access?

A: Computing, Storage, Database, and App services. 

---

Q: There are tow ways to create a User Pool. What are they?

A: Review defaults and Step through settings. 

---

Q: What is Anonymous Access?

A: It creates Identity pools for you. 