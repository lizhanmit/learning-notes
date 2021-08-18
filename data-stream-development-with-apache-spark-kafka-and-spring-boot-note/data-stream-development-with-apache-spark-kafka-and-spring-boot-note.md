# Data Stream Development with Apache Spark, Kafka, and Spring Boot Note 

- [Data Stream Development with Apache Spark, Kafka, and Spring Boot Note](#data-stream-development-with-apache-spark-kafka-and-spring-boot-note)
  - [Blueprint Architecture](#blueprint-architecture)
  - [Data Collection Tier](#data-collection-tier)
    - [Interaction Patterns](#interaction-patterns)
      - [Request Response Pattern](#request-response-pattern)
      - [Request Acknowledge Pattern](#request-acknowledge-pattern)
      - [Pub/Sub Pattern](#pubsub-pattern)
      - [One Way (Fire-and-Forget-Message) Pattern](#one-way-fire-and-forget-message-pattern)
      - [Stream Pattern](#stream-pattern)
    - [Protocols For Ingesting Data](#protocols-for-ingesting-data)
      - [Webhooks](#webhooks)
      - [HTTP Long Polling](#http-long-polling)
      - [Server-Sent Events (SSE)](#server-sent-events-sse)
      - [Server-Sent Events Push Proxy Variation](#server-sent-events-push-proxy-variation)
      - [WebSockets](#websockets)
      - [Comparison of Protocols](#comparison-of-protocols)
      - [Scaling Collection Tier and WebSocket Problem](#scaling-collection-tier-and-websocket-problem)
  - [Message Queuing Tier](#message-queuing-tier)
    - [What is the Point of Message Queuing Tier?](#what-is-the-point-of-message-queuing-tier)
  - [Between Collection and Message Queuing Tier](#between-collection-and-message-queuing-tier)
    - [Spring Cloud Stream](#spring-cloud-stream)
      - [Source, Processor & Sink Channel](#source-processor--sink-channel)
      - [Message Binders](#message-binders)
  - [Data Access Tier](#data-access-tier)
    - [Writing Analyzed Data in Long-Term Storage](#writing-analyzed-data-in-long-term-storage)
      - [Write Every Analyzed Single Data at Stream Speed 👎](#write-every-analyzed-single-data-at-stream-speed-)
      - [Write in Batch Model 👍](#write-in-batch-model-)
      - [Message Queuing and Batch Loader 👍👍](#message-queuing-and-batch-loader-)
    - [Query Data from Long-Term Storage](#query-data-from-long-term-storage)
    - [In-Memory Storage](#in-memory-storage)
    - [Caching Systems](#caching-systems)
      - [Read Caching Strategies](#read-caching-strategies)
      - [Write Caching Strategies](#write-caching-strategies)
    - [In-Memory DBs (IMDBs) & In-Memory Data Grids (IMDGs)](#in-memory-dbs-imdbs--in-memory-data-grids-imdgs)

---

**Use case: Analyzing Meetup RSVPs in Real-Time.**

---

## Blueprint Architecture

![meetup-rsvps-analyzer-architecture.png](img/meetup-rsvps-analyzer-architecture.png)

## Data Collection Tier 

Collection tier is built as a Spring Boot application that uses the Spring WebSocket API to communicate with the Meetup RSVP WebSocket endpoint.

### Interaction Patterns 

Interaction between data collection tier and data streaming API.

#### Request Response Pattern 

- Request response pattern (sync): This is a proper choice only when the potential delay is acceptable. For example, browse the web (navigate on Internet). Advantage: easy to implement.

![request-response-pattern.png](img/request-response-pattern.png)

- Client-async request response variation (half-async): The application can perform other tasks between a request-response cycle. This pattern is useful if your collection tier performs some extra tasks that are independent of the current request. It is **recommended** to rely on a framework that comes with asynchronous support, such as Netty, Play, or Node.js. Mainly, this will simplify and speed up the implementation time. 

![client-async-request-response-variation.png](img/client-async-request-response-variation.png)

- Full-async request response variation: 
In modern applications, this is preferable. It is **recommended** to rely on frameworks that comes with asynchronous capabilities.

![full-async-request-response-variation.png](img/full-async-request-response-variation.png)

#### Request Acknowledge Pattern 

No response is needed. 

Acknowledgement that the request was received successfully is needed. 

![request-acknowledge-pattern.png](img/request-acknowledge-pattern.png)

Acknowledgement can be eventually/optionally used for further requests. 

![request-acknowledge-pattern-reuse-ack.png](img/request-acknowledge-pattern-reuse-ack.png)

#### Pub/Sub Pattern 

Extremely used pattern in message-based data systems. 

![pub-sub-pattern.png](img/pub-sub-pattern.png)

#### One Way (Fire-and-Forget-Message) Pattern

This is useful when a system can trigger a request without expecting a response.

Relies only on requests. 

No responses. 

Useful when losing some data is acceptable. 

Example: sensors emitting data very fast (millisecond level).

![one-way-pattern.png](img/one-way-pattern.png)

#### Stream Pattern 

The collection tier triggers a single request to the data streaming API. This will result in a persistent connection between them. Further, the collection tier will consume the data as a stream, or, in other words, the response is a continuously flow of data.

![stream-pattern.png](img/stream-pattern.png)

**For the Meetup RSVPs use case, this is the interaction pattern that will be used.**

![interaction-pattern-used-for-meetup-rsvps.png](img/interaction-pattern-used-for-meetup-rsvps.png)

###  Protocols For Ingesting Data

#### Webhooks 

Webhook protocol relies on registering callbacks. 

![webhook-protocol.png](img/webhook-protocol.png)

Sometimes, step 1 is done manually by users filling up the needed information on a website, e.g. configuring Jenkins webhook on GitHub website.

Disadvantages: 

- The latency of data going over HTTP is average.
- Since it relies on HTTP POST request, it is not a good fit for high rate of updates. 
- This protocol is unidirectional, always from the streaming API to the collection tier, which means that supporting fault tolerance will be an issue. For example, we cannot acknowledge the streaming API that received the data.

Conclusion: this protocol has **low efficiency**. 

#### HTTP Long Polling

HTTP Long Polling holds each connection open till there is an update.

![http-long-polling.png](img/http-long-polling.png)

Advantages: 

- This protocol supports higher rate than Webhook protocol, like chat applications.

Disadvantages: 

- The latency of data going over HTTP is average.
- Since it relies on HTTP, it is not a good fit for high rate of updates. 
- Overhead of closing the connections and the collection tier has to reopen them.

Conclusion: Considering the rise of asynchronous programming in client side, this protocol has **average efficiency**.

#### Server-Sent Events (SSE)

Client connects to the server side.

Server side can push data the the client until the client closes the connection.

![server-sent-events.png](img/server-sent-events.png)

Advantages: 

- Since the network is used in a more efficient way, this protocol supports higher rate than HTTP Long Polling protocol.

Disadvantages: 

- Server-Sent Event is a unidirectional protocol based on HTTP. Average latency.
- No fault tolerance support. 

Conclusion: Good performance. Allows us to provide a more resilient API. **High efficiency**.

#### Server-Sent Events Push Proxy Variation 

Useful for mobile devices in order to save battery. 

Mobile delegates a proxy server to hold the connection open. 

Proxy will use a handset-specific push technology. 

Mobile devices wake to process the messages and then resume saving battery mode. 

![server-sent-events-push-proxy-variation.png](img/server-sent-events-push-proxy-variation.png)

#### WebSockets 

Full-duplex protocol that uses TCP for the communication transport. 

WebSockets use HTTP for the initial handshake and protocol upgrade request, and then switch to TCP. The streaming API sends updates to the collection tier via TCP.

The connection can be closed at any moment by the collection tier or the streaming API.

WebSockets use port 80 or 443 and URL scheme ws:// or wss://.

All major desktop and mobile browsers support WebSockets.

![websocket-protocol.png](img/websocket-protocol.png)

![websocket-protocol-2.png](img/websocket-protocol-2.png)

Advantages: 

- TCP is used for communication transport. Low latency, and supports high rate of updates. 
- Bidirectional from the beginning to the end. Allows us to build fault tolerant and reliable semantics into it.

Conclusion: This is one of the **most-used protocols for streaming**. **High efficiency**. 

**For the Meetup RSVPs use case, this is the protocol that will be used.**

#### Comparison of Protocols 

![comparison-of-protocols.png](img/comparison-of-protocols.png)

#### Scaling Collection Tier and WebSocket Problem

Problem: WebSockets are powerful for real-time applications, but more difficult to scale. It can be seen as below, the connection for the first instance is persistent while the other two instances are idle.

![websocket-protocol-scaling-problem.png](img/websocket-protocol-scaling-problem.png)

Solution 1: Add a buffer layer in the middle.

The single collection node is vertically scaled. 

![solution-1-add-a-buffer-layer.png](img/solution-1-add-a-buffer-layer.png)

Solution 2: Add a full-featured broker, such as Kafka or RabbitMQ. 

![solution-2-add-a-broker.png](img/solution-2-add-a-broker.png)

---

## Message Queuing Tier

### What is the Point of Message Queuing Tier? 

If there is no message queuing tier, it seems the data pipeline is faster and simpler. But there are coupled tiers. 

**Rule 1**: In distributed architectures, we want to decouple tiers.

Coupled tiers mean a lower level of abstraction. 

**Rule 2**: We strive for working with higher level of abstraction.

**Rule 3**: What is apparently simpler does not really mean that it is simpler.

Backpressure issue: 

![backpressure-issue.png](img/backpressure-issue.png)

Data durability issue: 

![data-durability-issue.png](img/data-durability-issue.png)

Data delivery semantics issue:

![data-delivery-semantics-issue.png](img/data-delivery-semantics-issue.png)

---

## Between Collection and Message Queuing Tier

### Spring Cloud Stream 

Spring Cloud Stream is a framework dedicated to microservice environments that relies on event/message-driven paradigm. It simplifies the writing of event/message-driven applications. 

It provides a pluggable Binder API to connect source, processor and sink channels to the middleware layer. 

![spring-cloud-stream.png](img/spring-cloud-stream.png)

#### Source, Processor & Sink Channel

![source-processor-sink-channel.png](img/source-processor-sink-channel.png)

#### Message Binders

Message binders are used for connecting applications to physical destinations. 

`@EnableBinding` turns Spring applications to Spring Cloud Stream applications, e.g. `@EnableBinding(Source.class)`.

![message-binder.png](img/message-binder.png)

![kafka-binder.png](img/kafka-binder.png)

Here producer and consumer are Spring Cloud Stream applications. 

Properties file `spring.cloud.stream.kafka.binder.brokers` is used for configuring producers and consumers. 

---

## Data Access Tier

When deciding to use what as the long-term storage, consider how you write data to and query from the storage.

### Writing Analyzed Data in Long-Term Storage

**For the Meetup RSVPs use case, MongoDB will be used as the long-term storage.**

#### Write Every Analyzed Single Data at Stream Speed 👎

- The long-term storage must be able to keep up with the stream speed. 
- This can be an issue when using RDBMS because each writing may require a new connection and/or transaction. 
- Performance of stream processing will slow.
- NoSQL DBs can be a good choice. For instance, Cassandra is a good fit for write-intensive applications.

#### Write in Batch Model 👍

- A batch acts as an in-memory buffer of data at stream speed.
- Better than directly writing at stream speed.
- Fits naturally for systems where data is loaded by batch and then processed, e.g. Spark. 
- Fits well for RDBMS and NoSQL DBs, which typically support batch-inserts. 

#### Message Queuing and Batch Loader 👍👍 

![message-queuing-and-batch-loader.png](img/message-queuing-and-batch-loader.png)

- Uses message queuing as an intermediary.
- Decouples analysis tier and supports the performance of stream processing. 

### Query Data from Long-Term Storage

- When data is perishable, Time-To-Live (TTL) can be used. MongoDB supports TTL feature. You have to set up the amount of time to keep the data (expiration time of the data).
- Reactive capable storages: If you try to deliver the data to the clients at the speed of arrival in the long-term storage, then you need to take into account the backpressure. Not all the clients will be capable to process the same amount of data per unit of time. In order to avoid the scenario in which the clients are overwhelmed by the amount of data, we can consider reactive capable storages such as MongoDB, Cassandra, and Redis.

### In-Memory Storage

Keep analyzed data in memory only when you need to access to it in real time. Otherwise, avoid in-memory embedded data stores, and better to rely on caching systems or in-memory DBs and grids.

Embedded data storage examples: 

- DerbyDB
- RocksDB
- SQLite
- H2
- HSQL
- LMDB

### Caching Systems

- Caching systems are optimized for managing data in-memory.
- Typically, they rely on key-value model. 
- Caching strategy is one of the key aspects.

#### Read Caching Strategies

- Read Through strategy

![read-through-strategy.png](img/read-through-strategy.png)

To avoid stale data, you need to set an eviction time for the cache, and invalidate data on updates.

- Cache Aside strategy

![cache-aside-strategy.png](img/cache-aside-strategy.png)

To avoid stale data, you need to set an eviction time for the cache, and invalidate data on updates.

- Read Ahead strategy

![read-ahead-strategy.png](img/read-ahead-strategy.png)

#### Write Caching Strategies

- Write Through strategy

![write-through-strategy.png](img/write-through-strategy.png)

- Write Back strategy

![write-back-strategy.png](img/write-back-strategy.png)

- Write around strategy
  
![write-around-strategy.png](img/write-around-strategy.png)

Only the data that is read and has a higher potential to be read again, it will be cached.

Caching system examples:

- Memcached
- Redis
- Hazelcast
- DynaCache
- EHCache
- OSCache

### In-Memory DBs (IMDBs) & In-Memory Data Grids (IMDGs)

- Use disk for data persistence whereas caching systems hold data in memory all the time.
- Use memory as the first option and disk as the second.

Examples: 

- Hazelcast
- Couchbase
- Apache Ignite
- Inifispan
- Apache Geode
- Aerospike

