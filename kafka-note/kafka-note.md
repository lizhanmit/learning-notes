# Kafka Note

- [Kafka Note](#kafka-note)
  - [Architecture](#architecture)
    - [ZooKeeper Ensemble](#zookeeper-ensemble)
  - [Overview](#overview)
  - [Commit Log](#commit-log)
  - [Topic, Partition & Message](#topic-partition--message)
    - [Messages](#messages)
    - [Partitions](#partitions)
    - [Topics](#topics)
  - [Broker](#broker)
  - [Producers](#producers)
    - [How Producers Write to Brokers](#how-producers-write-to-brokers)
    - [Producer Configuration](#producer-configuration)
      - [`acks`](#acks)
    - [Java Client Producer](#java-client-producer)
  - [Consumers](#consumers)
    - [Java Client Consumer](#java-client-consumer)
  - [Write & Read](#write--read)
  - [Various Source Code Packages](#various-source-code-packages)
    - [Kafka Streams](#kafka-streams)
    - [Kafka Connect](#kafka-connect)
    - [AdminClient](#adminclient)
  - [Kafka vs Flume](#kafka-vs-flume)
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

![kafka-architecture-3.png](img/kafka-architecture-3.png)

### ZooKeeper Ensemble

ZooKeeper helps maintain consensus in the cluster.

The reliance on ZooKeeper has been lessened with later client versions. In recent client versions, the only real interactions with ZooKeeper are brokers. Clients no longer store offsets in ZooKeeper.

ZooKeeper should have been running before you started your brokers. 

---

## Overview

Apache Kafka is publish-subscribe, high-throughput, distributed messaging system. 

Kafka scales very well in a horizontal way without compromising speed and efficiency.

Kafka is distributed, partitioned, replicated, and commit-log-based.

Small messages are not a problem for Kafka. The default message size is about 1 MB. It is configurable. 

Kafka's performance is effectively constant with respect to data size.

Kafka can process millions of messages quickly because it relies on the page cache instead of JVM heap. As a result, the brokers help avoid some of the issues that large heaps can hold, ie. long or frequent garbage collection pauses.

Whether data is coming from a database or a log event, my preference is to get the data into Kafka first. The data will be available in the purest form that you can achieve. Kafka does not really care about the content of the data or do any validation by default.

Two directives or purposes of Kafka: 

- Not block the producers (in order to deal with the back pressure). (the ability to capture messages even if the consuming service is down)
- Isolate producers and consumers. 

Three main capabilities: 

- Provide the ability to **publish/subscribe** to records like a **message queue**.
- Store records with **fault-tolerance**.
- Process **streams** as they occur.

Special features that make Kafka standout from other message brokers: 

- replayable messages 
- multiple consumers features

Application scenarios:

- messaging system
- website activity tracking - clickstream
- operational monitoring / IoT data processing - e.g. manufacturing facility, sensor data.
- log collection / aggregation
- stream processing - e.g. alerting abnormal usage when using credit card.

When Kafka might **NOT** be the right fit: 

- When you only need a once monthly or even once yearly summary of aggregate data.
- When you do not need an on-demand view, quick answer, or even the ability to reprocess data.
- Especially when data is manageable to process at once as a batch.
- When your main access pattern for data is mostly random lookup of data.
- When you need exact ordering of messages in Kafka for the entire topic.
- With larger messages, you start to see memory pressure increase.

Three types of Kafka clusters:

- Single node – single broker
- Single node – multiple broker
- Multiple node – multiple broker

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
- Exactly once (introduced in 0.11 release): The message is delivered exactly once. There is zero loss of any message.
  - If a message from a producer has a failure or is not acknowledged, the producer will resend the message.
  - The broker will only allow one message.
  - Consumers will only see the message once.

Kafka context overview: 

![kafka-context-overview.png](img/kafka-context-overview.png)

The answer to the questions below will impact various parts of implementation (configuration options): 

- Message loss: Is it okay to lose any messages in the system?
- Grouping: Does your data need to be grouped in any way? Are the events correlated with other events that are coming in?
- Ordering: Do you need data delivered in an exact order? What if a message gets delivered in an order other than when it actually occurred?
- Last value only: Do you only want the last value of a specific value? Or is history of that value important? Do you really care about the history of how your data values evolved over time? 
- Independent consumer: How many consumers are we going to have? Will they all be independent of each other or will they need to maintain some sort of order when reading the messages?

Kafka allows you to change key behaviors just by changing some configuration values.

---

## Commit Log

Commit log: the source of the truth.

As each new message comes in, it is added to the end of the log. The log is immutable and can only be appended to the end.

Users will use offsets to know where they are in that log.

Commit logs are retained by period of time or size configuration properties. In various companies, after the data in the Kafka commit logs hits a configurable size or time retention period, the data is often moved into a permanent store like S3 or HDFS.

When systems make changes, it creates events or changes. A log is like a never ending stream of these changes to a specific category or entity.

Change examples:
  - update to customer info
  - new orders
  - page views
  - scanning products

When one system updates the log, other systems can read from that log to sync themselves.

The message log can be compacted in two ways:

- coarse-grained: log compacted by time
- fine-grained: log compacted by message

---

## Topic, Partition & Message

![topic-partition-message.png](img/topic-partition-message.png)

Kafka splits a topic into partitions.

### Messages 

- Messages (= records) are byte arrays of data with String, JSON, and Avro being the most common.
- Each message consists of a key, a value and a timestamp. A key is not required.
- Each message is assigned a unique sequential identifier or key called **offset**. Messages with the same key arrive at the same partition.
- Messages are replicated across the cluster and persisted to disk.
- Kafka retains all messages for a configurable period of time.

### Partitions

Partition: how many parts you want the topic to be split into.

Each partition is an ordered immutable sequence of messages.

The partition is further broken up into **segment files** (the actual files) written on the disk drive.

A single partition only exists on one broker and will not be split between brokers.

One of the partition copies (replicas) will be the leader. Producers and consumers will only read or write from or to the leader.

### Topics 

![anatomy-of-a-topic.png](img/anatomy-of-a-topic.png)

Different topics can have different configurations of the amount of partitions.

There is configuration to enable or disable auto-creation of topics. However, you are free to manually create yourself.

You can use one topic as the starting point to
populate another topic. 

- Create a topic: `bin/kafka-topics.sh --zookeeper localhost: 2181 --create --topic helloworld --partitions 3 --replication-factor 3`
- List topics: `bin/kafka-topics.sh --zookeeper localhost:2181 --list`
- Describe a topic: `bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic helloworld`

![describe-a-topic.png](img/describe-a-topic.png)




---

## Broker

A single broker can handle several hundred megabytes of reads and writes per second from thousands of applications.

Each server acts as a leader for some of its partitions and a follower for others, so load is well balanced within the cluster.

![brokers-and-topic-partitions.png](img/brokers-and-topic-partitions.png)

If Broker 3 goes down,

- The lead partition of Topic 3 will move over to Broker 1.
- Broker 1 is little bit overloaded because it is handling writes for both Topic 1 and 3.
- Broker 2 will also adjust its partition of Topic 3.  

![broker-3-goes-down.png](img/broker-3-goes-down.png)

Multiple brokers: split topics across brokers into partitions. (replication for fault tolerance)

---

## Producers

There are no default producers.

A producer is also a way to send messages inside Kafka itself. For example, if you are reading data from a specific topic and wanted to send it to a different topic, you would also use a producer.

Start a console producer: `bin/kafka-console-producer.sh --broker-list localhost:9092 -- topic helloworld`

### How Producers Write to Brokers

![how-producers-write-to-brokers.png](img/how-producers-write-to-brokers.png)

Once the producer is connected to the cluster, it can then obtain metadata. The senders job includes fetching metadata about the cluster itself, which helps the producer find the information about which actual broker is written to (since producers only write to the leader of the partition).

The record accumulators job is to "accumulate" the messages into batches, which improves throughput and allow for a higher compression ratio than one messages at a time if compression for the messages is used. (If each and every message was sent one at a time, a slow down likely could be seen in regards to how fast your messages are being processed.)

**NOTE**: If one message in the batch fails, then the entire batch fails for that partition. Thus, there is already the built-in retry logic.

If ordering of the messages is important,

- set the retries to a non-zero number;
- set the `max.in.flight.requests.per.connection` <= 5;
- set `ack` to "all", which provides the **best** situation for making sure your producer’s messages arrive in the order.

Alternatively, set with the one configuration property `enable.idempotence`.

Do not have to worry about that one producer getting in the way of another producer’s data. Data will not be overwritten, but handled by the log itself and appended on the brokers log.

### Producer Configuration

#### `acks`

It controls how many acknowledgments the producer needs to receive from the leader before it returns a complete request.

Your message will not be seen by consumers until it is considered to be committed. However, this status is NOT related to the `acks` setting.

Set to `0`: 

- Lowest latency
- Cost of durability 
- Retries are not attempted. 
- Not a big deal in the situation where data loss is acceptable, e.g. web page click tracing.

Set to `1`:

- Guarantee that at least the leader has received the message. 
- The followers might not have copied the message before a failure brings down the leader.

Set to `all` or `-1`:

- The leader will wait on all of the in-sync replicas (ISRs) to acknowledge they have gotten the message. 
- Best for durability
- Not the quickest
- Suitable for the situation where data loss is not acceptable.  

### Java Client Producer

Pre-requisite: add the following dependency in pom.xml if using Maven.

```xml
<dependency>
  <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-clients</artifactId>
  <version>0.11.0.0</version>
</dependency>
```

Producers are thread-safe.

```java
/*
 * an example of a simple producer
 */

Properties props = new Properties();

// a list of message brokers
// best practice: include more than one server in case one of the servers is down or in maintenance
// this list does not have to be every server you have though, as after it connects, it will be able to find out information about the rest of the cluster brokers and will not depend on that list
props.put("bootstrap.servers", "localhost:9092,localhost:9093");  

// we provide a class that will be able to serialize the data as it moves into Kafka
// keys and values do not have to use the same serializer
props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

// producers are thread-safe
Producer<String, String> producer = new KafkaProducer<>(props);  

ProducerRecord producerRecord = new ProducerRecord<String, String>
("helloworld", null, "hello world again!");  // (topic, key, value)

producer.send(producerRecord);

producer.close();
```

---

## Consumers

Start a console consumer: `bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic helloworld --from-beginning`

### Java Client Consumer

Make sure you terminate the program after you done reading messages.

Java consumer client is **NOT** thread-safe.

```java
/*
 * an example of a simple consumer
 */

Properties props = new Properties();

// properties are set the same way as producers
props.put("bootstrap.servers", "localhost:9092,localhost:9093");
props.put("group.id", "helloconsumer");
props.put("enable.auto.commit", "true");
props.put("auto.commit.interval.ms", "1000");
props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");

KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);

// can subscribe to more than one topic at a time
consumer.subscribe(Arrays.asList("helloworld"));

while (true) {
  // no messages, one message, or many messages could all come back with a single poll
  ConsumerRecords<String, String> records = consumer.poll(100);
  for (ConsumerRecord<String, String> record : records)
    System.out.printf("offset = %d, key = %s, value = %s%n", record.offset(), record.key(), record.value());
}
```

---

## Write & Read

Linear read and writes is where Kafka shines.

**The leader handles all read and write requests** for the partition while the followers passively replicate the leader. 

Only committed messages are ever given out to the consumer. 

For producers, they have the option of either waiting for the message to be committed or not, depending on their preference for tradeoff between latency and durability.

One application reading a message off of the message brokers does not remove it from other applications that might want to consume it as well.

---

## Various Source Code Packages

### Kafka Streams 

Streams API was released in 2016.

lightweight library

Features: 

- local state with fault-tolerance
- one-at-a-time message processing
- exactly once delivery 

Streams API can be thought of an abstraction layer that sits on top of producers and consumers. 

It provides a higher level view of working with data as an unbounded stream.

It was made to make sure that creating streaming applications was as easy as possible and even provides a domain-specific language (DSL).

One of the sweet spots for Streams is
that no separate processing cluster is needed.

### Kafka Connect

This framework was created to make integration with other systems easier. It is already part of Kafka that really can make it simple to use pieces (connectors) that have been already been built to start your streaming journey.

The purpose of Connect is to help move data in or out of Kafka without having to deal with
writing our own producers and clients.

In many ways, it can be thought to help replace other tools such as Camus, Apache Gobblin, and Apache Flume. Using a direct comparison to Flume features are not the intention or sole goals of Connect.

Connectors: 

- Source connectors: import data from a source into Kafka.
- Sink connectors: export data out of Kafka into a different system.

Kafka Connect is great for making quick and simple data pipeline that tie together a common system.

If you use a database today and want to kick the tires on streaming data, one of the easiest on-ramps is to start with Kafka Connect.

Start standalone Connect for a file source: `bin/connect-standalone.sh config/connect-standalone.properties config/connect-file-source.properties`. Leave it running. 

Start standalone Connect for a file source and sink: `bin/connect-standalone.sh config/connect-standalone.properties config/connect-file-source.properties config/connect-file-sink.properties`

When to use Connect vs Kafka Clients? If you are not familiar enough with the upstream or downstream application, use a pre-built connector. 

Connect also has a REST API and provides options to build your own connector.

### AdminClient

With version 0.11.0, Kafka introduced the AdminClient API.

It is used to perform administrative actions.

---

## Kafka vs Flume

Kafka is designed for messages to be consumed by several applications.

Flume is designed to stream messages to a sink such as HDFS or HBase.

[Flume and Kafka Integration](https://github.com/lizhanmit/learning-notes/blob/master/flume-kafka-integration-note/flume-kafka-integration-note.md)

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
