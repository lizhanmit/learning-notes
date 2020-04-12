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

## CloudFormation 

The cloud formation stack helps you configure and maintain your system by provisioning and managing stacks of AWS resources based on a template.

Elastic Beanstalk creates the template for you.

---

## CloudFront

- Global content delivery network (CDN).
- Securely and quickly delivers data, video, applications, and APIs with low latency and high data transfer speeds.
- Global network of edge locations and regional edge caches.
- Ensures that end-user requests are served by the closest edge location: shorter distance = higher performance.
- Not cached? Persistent connections to origin servers for fast fetching.
- Using CloudFront is particularly helpful when performance is vital, especially on media.

Delivery method options:

- Web: for general use
- RTMP: for streaming media

---

## CloudWatch

- Helps with monitoring and management service.
- Provides data and actionable insights to determine health of your system.
- Logs, metrics, and events.
- Creates "high-resolution" alarms monitoring such as your costs and S3 buckets, and automated actions.

Workflow example: CloudWatch Alarm -> SNS -> SQS -> Lambda function

CloudWatch has two alarms to monitor loads, and they trigger when the alarms are too high or too low for the auto scaling group.

---

## DynamoDB

A NoSQL database service for all applications that need consistent, single-digit millisecond latency at any scale. 

The **primary key** is made up of a partition key (hash key) and an optional sort key. 

The **partition key** is used to partition data across hosts for scalability and availability. Choose an attribute which has a wide range of values and is likely to have evenly distributed access patterns.

**DynamoDB triggers** connect DynamoDB streams to Lambda functions. Whenever an item in the table is modified, a new stream record is written, which in turn triggers the Lambda function and causes it to execute.

---

## EC2

Elastic Compute Cloud (EC2): The virtual equivalent of the server computer. VM instance running Amazon Linux or Microsoft Windows Server configured for web apps.

Common uses: 

- As a web hosting server.
- Be good for any type of "processing" activity such as encoding and transcoding.

---

## Elastic Beanstalk (EB)

EB is used to deploy, monitor, and scale web apps and services quickly and easily.

It will create following components: 

- A EC2 instance.
- Security group: EC2 security configuration for port 80 HTTP ingress only. (needs VPC and does not create it)
- Auto Scaling group: configured to replace an instance if terminated or unavailable.
- S3 bucket: for source code, logs, and other artifacts created for Elastic Beanstalk needs.
- CloudWatch alarms: two to monitor load and triggers when too high or low for the Auto Scaling group.
- Domain name: routes to your web app subdomain.region.elasticbeanstalk.com

How to use: 

1. Select a platform.
2. Upload app or use sample.
3. Run it.

When you delete your app, the S3 bucket will not be deleted automatically. You need to go to "Permissions" of the S3 bucket -> "Bucket Policy", then delete "deny effect of deleting bucket by anyone" -> Save.

EB uses CloudFormation to manage resources.

---

## ElastiCache

A web service that makes it easier to launch, manage, and scale a distributed in-memory cache in the cloud.

help with performance

Cluster engine options:

- Redis
- Memcached (easier to set up)

---

## IAM

- users
- groups
- policies
- rules - services

**Good practice**: bond groups and policies, then add users to groups. 

### Cognito 

It offers: 

- User pools: provides sign-up and sign-in options for app users.
- Identity pools: provides AWS credentials to users to access AWS services.

Think of the **user pool** as a database for user accounts that allows them to login and the **identity pool** gets them into the right role to give them permissions into the rest of AWS.

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

SNS + SQS = Kafka

---

## Step Functions

It makes it easy to coordinate the components of distributed applications as a series of steps in a visual workflow. You can quickly build and run state machines to execute the steps of your application in a reliable and scalable fashion.

Based on concepts of tasks and state machines.

Tasks: Code (Lambda) or activity (waits for operator to perform sth.)

State machines are made up of states, their relationships, and the input and output defined by the Amazon States Language.

States make decisions based on input, perform actions, and pass output to other states.

Step Function examples: 

- Syncing/backing up S3 buckets
- Email verification/confirmation/authorization of process
- Scaling image automation

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

## Tips

Clean up resources from the top as much as possible, especially with CloudFormation stacks. 

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