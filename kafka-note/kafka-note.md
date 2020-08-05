# Kafka Note

- [Kafka Note](#kafka-note)
  - [Architecture](#architecture)
  - [Concepts](#concepts)
  - [Basics](#basics)
    - [Log](#log)
    - [Topic & Partition & Message](#topic--partition--message)
    - [Broker](#broker)
    - [Write & Read](#write--read)
  - [Kafka VS Flume](#kafka-vs-flume)
  - [Hardware Recommendations](#hardware-recommendations)
  - [Kafka Monitor](#kafka-monitor)
  - [Kafka Audit](#kafka-audit)
  - [Installation (kafka_2.11-0.9.0.0.tgz)](#installation-kafka_211-0900tgz)
    - [Config ZooKeeper](#config-zookeeper)
    - [Config Kafka](#config-kafka)
  - [How to Use Single Node Single Broker](#how-to-use-single-node-single-broker)
  - [How to Use Single Node Multiple Brokers](#how-to-use-single-node-multiple-brokers)

---

## Architecture

Typical connection way without Kafka:  

![typical-way-without-kafka.png](img/typical-way-without-kafka.png)

Using Kafka as a hub:

![using-kafka-as-a-hub.png](img/using-kafka-as-a-hub.png)

![kafka-as-data-exchange-hub.png](img/kafka-as-data-exchange-hub.png)

![pipeline-architecture-example.png](img/pipeline-architecture-example.png)

![kafka-architecture-2.png](img/kafka-architecture-2.png)

![kafka-architecture-3.png](img/kafka-architecture-3.png)

---

## Concepts

Apache Kafka is publish-subscribe, high-throughput, distributed messaging system. 

Kafka scales very well in a horizontal way without compromising speed and efficiency.

Kafka is distributed, partitioned, replicated, and commit-log-based.

Two directives or purposes of Kafka: 

- Not block the producers (in order to deal with the back pressure).
- Isolate producers and consumers. 

Three main capabilities: 

- Provide the ability to **publish/subscribe** to records like a **message queue**.
- Store records with **fault-tolerance**.
- Process **streams** as they occur.

Application scenarios:

- messaging system
- web analytics - clickstream
- operational monitoring - e.g. manufacturing facility, sensor data.
- log collection
- stream processing - e.g. alerting abnormal usage when using credit card.

Three types of Kafka clusters:

- Single node–single broker
- Single node–multiple broker
- Multiple node–multiple broker

**Three ways to deliver messages**:

- At least once (**by default**): The messages are never lost. 
  - If a message from a producer has a failure or is not acknowledged, the producer will resend the message.
  - The broker will see two messages (or only one if there was a true failure).
  - Consumers will get as many messages as the broker received. They might have to deduplicate messages.
- At most once: The messages may be lost.
  - If a message from a producer has a failure or is not acknowledged, the producer will **not** resend the message.
  - The broker will see one message at most (or zero if there was a true failure).
  - Consumers will see the messages that the broker received. If there was a failure, the consumer would never see that message.
  - Q: Why would someone be OK with losing a message? A: **Keeping the system performing and not waiting on acknowledgements might outweigh any gain from lost data.**
- Exactly once: The message is delivered exactly once. There is zero loss of any message.
  - If a message from a producer has a failure or is not acknowledged, the producer will resend the message.
  - The broker will only allow one message.
  - Consumers will only see the message once.

The message log can be compacted in two ways:

- Coarse-grained: Log compacted by time
- Fine-grained: Log compacted by message

---

## Basics

### Log

- Log: The source of the truth.
- When systems make changes, it creates events or changes. A log is like a never ending stream of these changes to a specific category or entity.
  - Changes:
    - update to customer info
    - new orders
    - page views
    - scanning products
- When one system updates the log, other systems can read from that log to sync themselves.

### Topic & Partition & Message

![topic-partition-message.png](img/topic-partition-message.png)

- Kafka splits a topic into partitions.
- Each partition is an ordered immutable sequence of messages.
- Messages are byte arrays of data with String, JSON, and Avro being the most common.
- Messages are replicated across the cluster and persisted to disk.
- Each message is assigned a unique sequential identifier or key called **offset**. Messages with the same key arrive at the same partition.
- Each message consists of a key, a value and a timestamp.
- Kafka retains all messages for a configurable period of time.
- Kafka's performance is effectively constant with respect to data size.

![anatomy-of-a-topic.png](img/anatomy-of-a-topic.png)

Different topics can have different configurations of the amount of partitions.

Each server acts as a leader for some of its partitions and a follower for others, so load is well balanced within the cluster.

### Broker

A single broker can handle several hundred megabytes of reads and writes per second from thousands of applications.

![brokers-and-topic-partitions.png](img/brokers-and-topic-partitions.png)

If Broker 3 goes down,

- The lead partition of Topic 3 will move over to Broker 1.
- Broker 1 is little bit overloaded because it is handling writes for both Topic 1 and 3.
- Broker 2 will also adjust its partition of Topic 3.  

![broker-3-goes-down.png](img/broker-3-goes-down.png)

Multiple brokers: split topics across brokers into partitions. (replication for fault tolerance)

### Write & Read

**The leader handles all read and write requests** for the partition while the followers passively replicate the leader. 

Only committed messages are ever given out to the consumer. 

For producers, they have the option of either waiting for the message to be committed or not, depending on their preference for tradeoff between latency and durability.

One application reading a message off of the message brokers doesn’t remove it from other applications that might want to consume it as well.

---

## Kafka VS Flume

Kafka is designed for messages to be consumed by several applications.

Flume is designed to stream messages to a sink such as HDFS or HBase.

---

## Hardware Recommendations

It is **not** common to have Kafka running actively across multiple data centers due to latency and throughput issues.  

Hardware recommendations for machines running Kafka.

Machine

- medium-sized

Memory

- 64 GB RAM - decent choice
- 32 GB RAM - recommend

CPU

- 24 cores per machine

Disk

- multiple log directories
- each directory is mounted on a separate drive
- fast

Network

- 1~10 GB ethernet

---

## Kafka Monitor

Use Kafka Monitor to run **long-running tests** to monitor the Kafka cluster.

![kafka-monitor.png](img/kafka-monitor.png)

---

## Kafka Audit

Verify all the messages are being handled properly, and make sure that they are being delivered and processed and everything is in sync, and you are not losing messages or data.

Options:

- Kafka Audit (Roll your own)
- Chaperone (Uber)
- Confluent Control Center

---

## Installation (kafka_2.11-0.9.0.0.tgz)

### Config ZooKeeper

Kafka uses ZooKeeper so start ZooKeeper firstly.

1. Under `zookeeper/conf`, create zoo.cfg file based on zoo_sample.cfg.
2. In zoo.cfg file, modify `dataDir=/tmp/zookeeper` to `dataDir=/usr/local/zookeeper/tmp`, as the former directory will be clear every time when the server starts.
3. Start ZooKeeper. Under `zookeeper/bin`, command line: `zkServer.sh start`.

### Config Kafka

1. Under `kafka_2.11-0.9.0.0/config`, in server.properties file, uncomment `host.name=localhost`, modify `log.dirs=/tmp/kafka-logs` to `log.dirs=/usr/local/kafka_2.11-0.9.0.0/tmp`, as the former directory will be clear every time when the server starts.
2. Start Kafka. Under `kafka_2.11-0.9.0.0`, command line: `bin/kafka-server-start.sh config/server.properties`.

---

## How to Use Single Node Single Broker

Useful config ($KAFKA_HOME/config/server.properties):

- broker.id=0
- listeners
- host.name
- log.dirs
- zookeeper.connect

Steps:

1. Create a topic: In terminal A, `bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic hello_topic`.
2. Check the topic: `bin/kafka-topics.sh --list --zookeeper localhost:2181`. Then "hello_topic" will be displayed.
3. Produce messages: In terminal B, `bin/kafka-console-producer.sh --broker-list localhost:9092 --topic hello_topic`.
4. Consume messages: In terminal C, `bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic hello_topic --from-beginning`. **Note here:** `--from beginning` means consuming messages including all already sent in terminal B. Use it if you want all messages from the beginning; otherwise do not use it.
5. Type something in terminal B, they will be displayed in terminal C.

> Check all topics info: `bin/kafka-topics.sh --describe --zookeeper localhost:2181`.

> Check a specific topic (here it is hello_topic) info: `bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic hello_topic`.

> Add partitions: `bin/kafka-topics.sh --alter --zookeeper localhost:2181 --partitions <number_of_partitions> --topic hello_topic`.

> Delete a topic:
> Topic deletion is disabled by default.
> To enable: `delete.topic.enable=true`.
> `bin/kafka-topics.sh --delete --zookeeper localhost:2181 --topic hello_topic`.

---

## How to Use Single Node Multiple Brokers

Refer to Kafka website (http://kafka.apache.org/quickstart#quickstart_multibroker).
