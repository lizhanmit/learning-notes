# Architecting Big Data Applications Note

## Real-Time Applications

### Real-Time

- As the response time expectations get higher, or the target response times get lower, the cost increases exponentially.
- Go as high as possible for the response time value without impacting user experience or application effectiveness.

Synchronous vs. Asynchronous Pipelines

![synchronous-vs-asynchronous-pipelines.png](img/synchronous-vs-asynchronous-pipelines.png)

Strategy:

- Use asynchronous pipelines wherever possible.
- **DO NOT** use synchronous pipelines unless absolutely required.
- Build horizontally scalable systems that maximize **parallel processing**.
- Use **buffering queues** between producers and consumers to adjust for differences in throughput.
- Service components in the architecture should be **stateless**.
- If state needs to be stored, use database, in memory data grids or clusters.
- Every request in a real-time pipeline has a time to live, after which, it goes stale or out of context. Monitor it and drop the request if it exceeds.

## Use Cases

### Social Media Sentiment Analysis

#### Problem

Business needs an overall real-time (a few minutes) tracking board and list of negative posts and posters.

#### Solution

![social-media-sentiment-analysis-solution.png](img/social-media-sentiment-analysis-solution.png)

- You can create separate subscription threads for each of the hashtags.
- One single Kafka topic.
- The number of Spark and Kafka partitions should be the same.

#### Technologies

##### Stream Processing

![social-media-sentiment-analysis-technologies.png](img/social-media-sentiment-analysis-technologies.png)

- Each post can be processed independently.
- Use map operations to cleanse text.
- Use sentiment analysis engine.
- Use reduce to summarize tweets by sentiment.
- Keep batch intervals as high as possible.

##### Streaming Message Queues

- [ ] RabbitMQ
- [ ] Apache ActiveMQ
- [x] Apache Kafka (choose this one because of excellent integration with Apache Spark)
(equivalent capabilities)

##### Real-Time Subscribers

- Build customized subscribers to subscribe to messages in real time, and then push them to Kafka.

##### Sentiment Analysis Engine

- Build a web service (application) based on Python libraries (NTLK package).
- Scale by using multiple web servers behind a load balancer.

##### Message Database

- MySQL can easily handle 100,000 records per day.
- If the number of records increases in the future, you may need to use NoSQL.  
