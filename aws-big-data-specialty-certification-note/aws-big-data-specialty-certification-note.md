# AWS Big Data Specialty Certification Note

- [AWS Big Data Specialty Certification Note](#aws-big-data-specialty-certification-note)
  - [Simple Queue Service (SQS)](#simple-queue-service-sqs)
    - [Overview](#overview)
    - [SQS Queue](#sqs-queue)
    - [SQS Use Cases](#sqs-use-cases)
    - [SQS Limits](#sqs-limits)
    - [SQS Security](#sqs-security)
    - [**Exam Preparation**](#exam-preparation)
    - [Kinesis VS. SQS](#kinesis-vs-sqs)
  - [IoT](#iot)
    - [Overview](#overview-1)
    - [Device Gateway](#device-gateway)
    - [Message Broker](#message-broker)
    - [Thing Registry](#thing-registry)
    - [Authentication](#authentication)
    - [Authorization](#authorization)
    - [Device Shadow](#device-shadow)
    - [Rules Engine](#rules-engine)
    - [Greengrass](#greengrass)
    - [**Exam Preparation**](#exam-preparation-1)
  - [Database Migration Service (DMS)](#database-migration-service-dms)
  - [Direct Connect](#direct-connect)
  - [Snow Family](#snow-family)
    - [Data Migration](#data-migration)
    - [Snowball Edge](#snowball-edge)
    - [Snowcone](#snowcone)
    - [Snowmobile](#snowmobile)
    - [Data Migration Comparison](#data-migration-comparison)
    - [Usage Process](#usage-process)
    - [Edge Computing](#edge-computing)
    - [OpsHub](#opshub)
  - [Managed Streaming for Apache Kafka (MSK)](#managed-streaming-for-apache-kafka-msk)
    - [Overview](#overview-2)
    - [Configuration](#configuration)
    - [Security](#security)
    - [Monitoring](#monitoring)
    - [Kinesis Data Streams VS. MSK](#kinesis-data-streams-vs-msk)
    - [**Exam Preparation**](#exam-preparation-2)
  - [Simple Storage Service (S3)](#simple-storage-service-s3)
    - [Overview](#overview-3)
    - [Consistency Model](#consistency-model)
    - [Storage Classes](#storage-classes)
    - [Lifecycle Rules](#lifecycle-rules)
    - [Versioning](#versioning)
    - [Replication](#replication)
    - [Performance](#performance)
    - [Encryption](#encryption)
    - [Security](#security-1)
    - [S3 Select & Glacier Select](#s3-select--glacier-select)
    - [Event Notifications](#event-notifications)
  - [DynamoDB](#dynamodb)
    - [Overview](#overview-4)
    - [Primary Keys](#primary-keys)
    - [Use Cases](#use-cases)
    - [Provisioned Throughput](#provisioned-throughput)
    - [Partitions Internal](#partitions-internal)
    - [APIs](#apis)
    - [Indexes](#indexes)

---

## Simple Queue Service (SQS)

### Overview

![sqs.png](img/sqs.png)

### SQS Queue

![sqs-standard-queue.png](img/sqs-standard-queue.png)

![sqs-producing-messages.png](img/sqs-producing-messages.png)

![sqs-consuming-messages.png](img/sqs-consuming-messages.png)

![sqs-fifo-queue.png](img/sqs-fifo-queue.png)

![sqs-extended-client.png](img/sqs-extended-client.png)

### SQS Use Cases

![sqs-use-cases.png](img/sqs-use-cases.png)

### SQS Limits

![sqs-limits.png](img/sqs-limits.png)

### SQS Security

![sqs-security.png](img/sqs-security.png)

### **Exam Preparation**

SQS is a small topic in the exam. Do not need to remember all details. Just having a general idea is enough.

**You need to know when to use Kinesis and when to use SQS. (real important)**

### Kinesis VS. SQS

![kinesis-vs-sqs.png](img/kinesis-vs-sqs.png)

![kinesis-vs-sqs-2.png](img/kinesis-vs-sqs-2.png)

![kinesis-vs-sqs-3.png](img/kinesis-vs-sqs-3.png)

---

## IoT

### Overview

![iot-overview.png](img/iot-overview.png)

Device Shadow: When the IoT device is not connected with AWS Cloud, the data will be updated at Device Shadow. After the device is connected again, the update will be synced.

### Device Gateway

![iot-device-gateway.png](img/iot-device-gateway.png)

### Message Broker

![iot-message-broker.png](img/iot-message-broker.png)

### Thing Registry

![iot-thing-registry.png](img/iot-thing-registry.png)

### Authentication

![iot-authentication.png](img/iot-authentication.png)

### Authorization

![iot-authorization.png](img/iot-authorization.png)

### Device Shadow

![iot-device-shadow.png](img/iot-device-shadow.png)

### Rules Engine

![iot-rules-engine.png](img/iot-rules-engine.png)

### Greengrass

![iot-greengrass.png](img/iot-greengrass.png)

### **Exam Preparation**

IoT is a quite light subject in the exam. Do not need to remember all details. Just having a general idea is enough.

Q: How to get IoT devices send data to Kinesis? 

A: Do not directly put records on Kinesis. Instead, send records to IoT topic, and define IoT Rule Action, then send to Kinesis. 

---

## Database Migration Service (DMS)

![dms.png](img/dms.png)

![dms-sources-and-targets.png](img/dms-sources-and-targets.png)

![sct.png](img/sct.png)

---

## Direct Connect

![direct-connect.png](img/direct-connect.png)

![direct-connect-diagram.png](img/direct-connect-diagram.png)

![direct-connect-gateway.png](img/direct-connect-gateway.png)

---

## Snow Family

They are hardware, physical devices. 

1. Get the Snow Family devices from AWS via delivery, e.g. post offices. 
2. You load your data onto those devices. 
3. You ship the devices back to AWS facility. 
4. AWS plugs the devices to their infrastructure. Then the data will be migrated to AWS Cloud. 

![snow-family.png](img/snow-family.png)

### Data Migration

![snow-family-data-migration.png](img/snow-family-data-migration.png)

![direct-upload-vs-using-snow-family.png](img/direct-upload-vs-using-snow-family.png)

### Snowball Edge

![snowball-edge.png](img/snowball-edge.png)

### Snowcone

Snowcone is much smaller than Snowball Edge.

![snowcone.png](img/snowcone.png)

### Snowmobile

![snowmobile.png](img/snowmobile.png)

### Data Migration Comparison

![snow-family-data-migration-2.png](img/snow-family-data-migration-2.png)

### Usage Process

![snow-family-usage-process.png](img/snow-family-usage-process.png)

### Edge Computing

![edge-computing.png](img/edge-computing.png)

![snow-family-edge-computing.png](img/snow-family-edge-computing.png)

### OpsHub

![aws-opshub.png](img/aws-opshub.png)

---

## Managed Streaming for Apache Kafka (MSK)

### Overview 

![msk.png](img/msk.png)

Compared with Kinesis, MSK is more configurable, which allows you to get around Kinesis limits.  

![msk-high-level.png](img/msk-high-level.png)

### Configuration

![msk-configuration.png](img/msk-configuration.png)

### Security

![msk-security.png](img/msk-security.png)

### Monitoring

![msk-monitoring.png](img/msk-monitoring.png)

### Kinesis Data Streams VS. MSK

![kinesis-data-streams-vs-msk.png](img/kinesis-data-streams-vs-msk.png)

### **Exam Preparation**

The exam will mainly try to make you choose between Kafka and Kinesis based on some scenarios. Most likely, you will have to choose Kinesis Data Streams over MSK because AWS would like you to use their products. But maybe in one or two edge cases, MSK is the right choice.

---

## Simple Storage Service (S3)

### Overview

![s3-overview-buckets.png](img/s3-overview-buckets.png)

![s3-overview-objects.png](img/s3-overview-objects.png)

### Consistency Model

![s3-consistency-model.png](img/s3-consistency-model.png)

### Storage Classes

![s3-storage-classes.png](img/s3-storage-classes.png)

![s3-standard-general-purpose.png](img/s3-standard-general-purpose.png)

![s3-standard-ia.png](img/s3-standard-ia.png)

![s3-one-zone-ia.png](img/s3-one-zone-ia.png)

![s3-intelligent-tiering.png](img/s3-intelligent-tiering.png)

![amazon-glacier.png](img/amazon-glacier.png)

![amazon-glacier-and-deep-archive.png](img/amazon-glacier-and-deep-archive.png)

![s3-storage-classes-comparison.png](img/s3-storage-classes-comparison.png)

![s3-storage-classes-price-comparison.png](img/s3-storage-classes-price-comparison.png)

![s3-moving-between-storage-classes.png](img/s3-moving-between-storage-classes.png)

### Lifecycle Rules

![s3-lifecycle-rules.png](img/s3-lifecycle-rules.png)

![s3-lifecycle-rules-scenario-1.png](img/s3-lifecycle-rules-scenario-1.png)

![s3-lifecycle-rules-scenario-2.png](img/s3-lifecycle-rules-scenario-2.png)

### Versioning

![s3-versioning.png](img/s3-versioning.png)

### Replication

![s3-replication.png](img/s3-replication.png)

![s3-replication-notes.png](img/s3-replication-notes.png)

### Performance

![s3-baseline-performance.png](img/s3-baseline-performance.png)

![s3-kms-limitation.png](img/s3-kms-limitation.png)

![s3-performance.png](img/s3-performance.png)

![s3-performance-byte-range-fetches.png](img/s3-performance-byte-range-fetches.png)

### Encryption

![s3-encryption.png](img/s3-encryption.png)

![s3-sse-s3.png](img/s3-sse-s3.png)

![s3-sse-kms.png](img/s3-sse-kms.png)

![s3-sse-c.png](img/s3-sse-c.png)

![s3-client-side-encryption.png](img/s3-client-side-encryption.png)

![encryption-in-transit.png](img/encryption-in-transit.png)

### Security

![s3-security.png](img/s3-security.png)

![s3-bucket-policies.png](img/s3-bucket-policies.png)

![s3-bucket-settings-for-block-public-access.png](img/s3-bucket-settings-for-block-public-access.png)

![s3-security-other.png](img/s3-security-other.png)

### S3 Select & Glacier Select

![s3-select-and-glacier-select.png](img/s3-select-and-glacier-select.png)

### Event Notifications

![s3-event-notifications.png](img/s3-event-notifications.png)

---

## DynamoDB

### Overview

![dynamodb.png](img/dynamodb.png)

![dynamodb-basics.png](img/dynamodb-basics.png)

### Primary Keys

![dynamodb-primary-keys.png](img/dynamodb-primary-keys.png)

![dynamodb-primary-keys-2.png](img/dynamodb-primary-keys-2.png)

### Use Cases

![dynamodb-use-cases.png](img/dynamodb-use-cases.png)

### Provisioned Throughput

![dynamodb-provisioned-throughput.png](img/dynamodb-provisioned-throughput.png)

![dynamodb-wcu.png](img/dynamodb-wcu.png)

![strongly-vs-eventually-consistent-read.png](img/strongly-vs-eventually-consistent-read.png)

![dynamodb-rcu.png](img/dynamodb-rcu.png)

![dynamodb-throttling.png](img/dynamodb-throttling.png)

### Partitions Internal

![dynamodb-partitions-internal.png](img/dynamodb-partitions-internal.png)

### APIs

![dynamodb-writing-data.png](img/dynamodb-writing-data.png)

![dynamodb-deleting-data.png](img/dynamodb-deleting-data.png)

![dynamodb-batching-writes.png](img/dynamodb-batching-writes.png)

![dynamodb-reading-data.png](img/dynamodb-reading-data.png)

![dynamodb-query.png](img/dynamodb-query.png)

![dynamodb-scan.png](img/dynamodb-scan.png)

### Indexes

(really important)

![dynamodb-lsi.png](img/dynamodb-lsi.png)

![dynamodb-gsi.png](img/dynamodb-gsi.png)




