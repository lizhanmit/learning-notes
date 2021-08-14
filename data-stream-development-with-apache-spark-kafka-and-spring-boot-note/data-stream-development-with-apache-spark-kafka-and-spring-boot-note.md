# Data Stream Development with Apache Spark, Kafka, and Spring Boot Note 

**Use case: Analyzing Meetup RSVPs in Real-Time.**

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

**For the use case, this is the interaction pattern that will be used for Meetup RSVPs.**

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

#### Server-Sent Events

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

