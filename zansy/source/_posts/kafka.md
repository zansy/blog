title: Apache Kafka Notes(2021/07/08 updated 1/9)
author: zansy
toc: true
tags:
  - Kafka
categories:
  - 唯有爱与工作不可辜负
date: 2021-07-08 21:13:00
---
Apache Kafka is an open-source distributed event streaming platform for high-performance data pipelines, streaming analytics, data integration, and mission-critical applications. Notes by Kafka official website.
<!--more-->
# 1. GETTING STARTED
## 1.1 Introduction
Kafka combines three key capabilities so you can implement your use cases for event streaming end-to-end with a single battle-tested solution:

- To publish (write) and subscribe to (read) streams of events, including continuous import/export of your data from other systems.
- To store streams of events durably and reliably for as long as you want.
- To process streams of events as they occur or retrospectively.

And all this functionality is provided in a distributed, highly scalable, elastic, fault-tolerant, and secure manner. Kafka can be deployed on bare-metal hardware, virtual machines, and containers, and on-premises as well as in the cloud.

### How does Kafka work
Kafka is a distributed system consisting of servers and clients that communicate via a high-performance TCP network protocol. 

**Servers**: Kafka is run as a cluster of one or more servers that can span multiple datacenters or cloud regions. Some of these servers form the storage layer, called the brokers. Other servers run Kafka Connect to continuously import and export data as event streams to integrate Kafka with your existing systems such as relational databases as well as other Kafka clusters. To let you implement mission-critical use cases, a Kafka cluster is highly scalable and fault-tolerant: if any of its servers fails, the other servers will take over their work to ensure continuous operations without any data loss.

**Clients**: They allow you to write distributed applications and microservices that read, write, and process streams of events in parallel, at scale, and in a fault-tolerant manner even in the case of network problems or machine failures. Kafka ships with some such clients included, which are augmented by dozens of clients provided by the Kafka community: clients are available for Java and Scala including the higher-level Kafka Streams library, for Go, Python, C/C++, and many other programming languages as well as REST APIs.

### Concepts
An **event** is also called record or message in the documentation. When you read or write data to Kafka, you do this in the form of events. Conceptually, an event has a key, value, timestamp, and optional metadata headers.

**Producers** are those client applications that publish (write) events to Kafka, and **consumers** are those that subscribe to (read and process) these events. In Kafka, producers and consumers are fully decoupled and agnostic of each other, which is a key design element to achieve the high scalability that Kafka is known for.

Events are organized and durably stored in **topics**. Very simplified, a topic is similar to a folder in a filesystem, and the events are the files in that folder. An example topic name could be "payments". Topics in Kafka are always multi-producer and multi-subscriber: a topic can have zero, one, or many producers that write events to it, as well as zero, one, or many consumers that subscribe to these events. Events in a topic can be read as often as needed—unlike traditional messaging systems, events are not deleted after consumption. Instead, you define for how long Kafka should retain your events through a per-topic configuration setting, after which old events will be discarded. Kafka's performance is effectively constant with respect to data size, so storing data for a long time is perfectly fine.

Topics are **partitioned**, meaning a topic is spread over a number of "buckets" located on different Kafka brokers. This distributed placement of your data is very important for scalability because it allows client applications to both read and write the data from/to many brokers at the same time.

![streams-and-tables-p1_p4](/images/kafka/streams-and-tables-p1_p4.png)

To make your data fault-tolerant and highly-available, every topic can be **replicated**, even across geo-regions or datacenters, so that there are always multiple brokers that have a copy of the data just in case things go wrong, you want to do maintenance on the brokers, and so on. A common production setting is a replication factor of 3, i.e., there will always be three copies of your data. This replication is performed at the level of topic-partitions.

## 1.2 Use Cases
- Messaging
  In this domain Kafka is comparable to traditional messaging systems such as ActiveMQ or RabbitMQ.
- Website Activity Tracking
  The original use case for Kafka was to be able to rebuild a user activity tracking pipeline as a set of real-time publish-subscribe feeds. This means site activity (page views, searches, or other actions users may take) is published to central topics with one topic per activity type.
- Metrics
- Log Aggregation
- Stream Processing
- Event Sourcing
- Commit Log

## 1.3 Quick Start
```bash
$ tar -xzf kafka_2.13-2.8.0.tgz
$ cd kafka_2.13-2.8.0

# 1. START THE KAFKA ENVIRONMENT
# Start the ZooKeeper service
# Note: Soon, ZooKeeper will no longer be required by Apache Kafka.
$ bin/zookeeper-server-start.sh config/zookeeper.properties
# Start the Kafka broker service
$ bin/kafka-server-start.sh config/server.properties

# 2. CREATE A TOPIC TO STORE YOUR EVENTS
# Before you can write your first events, you must create a topic.
$ bin/kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092

# 3. WRITE SOME EVENTS INTO THE TOPIC
$ bin/kafka-console-producer.sh --topic quickstart-events --bootstrap-server localhost:9092
This is my first event
This is my second event
# You can stop the producer client with Ctrl-C at any time.

# 4. READ THE EVENTS
# Open another terminal session and run the console consumer client to read the events you just created:
$ bin/kafka-console-consumer.sh --topic quickstart-events --from-beginning --bootstrap-server localhost:9092
This is my first event
This is my second event
```
> Feel free to experiment: for example, switch back to your producer terminal (previous step) to write additional events, and see how the events immediately show up in your consumer terminal.

> Because events are durably stored in Kafka, they can be read as many times and by as many consumers as you want. You can easily verify this by opening yet another terminal session and re-running the previous command again.


6. IMPORT/EXPORT YOUR DATA AS STREAMS OF EVENTS WITH KAFKA CONNECT

    Kafka Connect allows you to continuously ingest data from external systems into Kafka, and vice versa. It is thus very easy to integrate existing systems with Kafka. 
7. PROCESS YOUR EVENTS WITH KAFKA STREAMS
   Once your data is stored in Kafka as events, you can process the data with the Kafka Streams client library for Java/Scala. It allows you to implement mission-critical real-time applications and microservices, where the input and/or output data is stored in Kafka topics.
   
   Here's how one would implement the popular "WordCount" algorithm:
   ```Java
   KStream<String, String> textLines = builder.stream("quickstart-events");
    KTable<String, Long> wordCounts = textLines
            .flatMapValues(line -> Arrays.asList(line.toLowerCase().split(" ")))
            .groupBy((keyIgnored, word) -> word)
            .count();
    wordCounts.toStream().to("output-topic", Produced.with(Serdes.String(), Serdes.Long()));
   ```
8. TERMINATE THE KAFKA ENVIRONMENT
    1. Stop the producer and consumer clients with Ctrl-C, if you haven't done so already.
    2. Stop the Kafka broker with Ctrl-C.
    3. Lastly, stop the ZooKeeper server with Ctrl-C.
    4. 
    ```bash
    # If you also want to delete any data of your local Kafka environment including any events you have created along the way, run the command:
    $ rm -rf /tmp/kafka-logs /tmp/zookeeper
    ```

## 1.4 Ecosystem
[Here is a list of tools about integrate with Kafka outside the main distribution](https://cwiki.apache.org/confluence/display/KAFKA/Ecosystem)

# 2. APIS

# 8. KAFKA CONNECT
## 8.1 Overview
Kafka Connect is a tool for scalably and reliably streaming data between Apache Kafka and other systems. It makes it simple to quickly define connectors that move large collections of data into and out of Kafka. Kafka Connect can ingest entire databases or collect metrics from all your application servers into Kafka topics, making the data available for stream processing with low latency. 

Kafka Connect features include:

- **A common framework for Kafka connectors** - Kafka Connect standardizes integration of other data systems with Kafka, simplifying connector development, deployment, and management
Distributed and standalone modes - scale up to a large, centrally managed service supporting an entire organization or scale down to development, testing, and small production deployments
- **REST interface** - submit and manage connectors to your Kafka Connect cluster via an easy to use REST API
- **Automatic offset management** - with just a little information from connectors, Kafka Connect can manage the offset commit process automatically so connector developers do not need to worry about this error prone part of connector development
- **Distributed and scalable by default** - Kafka Connect builds on the existing group management protocol. More workers can be added to scale up a Kafka Connect cluster.
- **Streaming/batch integration** - leveraging Kafka's existing capabilities, Kafka Connect is an ideal solution for bridging streaming and batch data systems

## 8.2 User Guide
