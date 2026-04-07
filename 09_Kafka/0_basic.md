# Apache Kafka – Complete Detailed Notes (Beginner to Production Level)

---

# 1. What is Kafka?

Apache Kafka is a **distributed event streaming platform** used to build real-time data pipelines and streaming applications.

It allows applications to:

* Publish data (Producer)
* Store data (Broker)
* Consume data (Consumer)

Kafka is NOT a traditional database.
It is a **distributed, partitioned, append-only commit log system**.

---

# 2. Why Kafka?

Kafka is used when:

* Systems need real-time communication
* Microservices must be decoupled
* High throughput is required
* Scalability is important
* Fault tolerance is required

Common Use Cases:

* Order processing systems
* Payment systems
* Log aggregation
* Event-driven architecture
* Real-time analytics
* Notification systems
* IoT streaming

---

# 3. Core Components

## 3.1 Producer

Application that sends messages to Kafka.

Example:

```
kafkaTemplate.send("orders", orderData);
```

Producer sends data to a Topic.

---

## 3.2 Topic

A logical category where messages are stored.

Examples:

* orders
* payments
* notifications

Topics are divided into partitions.

---

## 3.3 Partition

Each topic is divided into partitions.

Why partitions?

* Parallel processing
* Scalability
* High throughput

Important:

* Ordering is guaranteed ONLY inside a partition.
* Not guaranteed across partitions.

Example:
Topic: orders
Partitions: 0, 1, 2

Each partition maintains its own sequential log.

---

## 3.4 Broker

A Kafka server.

Multiple brokers form a Kafka Cluster.

Each broker stores partitions.

---

## 3.5 Consumer

Application that reads messages from Kafka.

Consumers belong to a Consumer Group.

---

# 4. How Kafka Stores Data

Kafka stores data as:

Topic → Partition → Log File (on disk)

Each partition is:

* Append-only
* Sequentially written
* Stored as segment files

Kafka does NOT use SQL or NoSQL tables.
It uses log-based storage.

Because writes are sequential, Kafka is very fast.

---

# 5. Offset (Very Important)

Every message inside a partition has a number called an OFFSET.

Example:
0, 1, 2, 3, 4...

Consumer keeps track of last committed offset.

Offset acts like a bookmark.

---

# 6. Producer Flow

1. Business function executes
2. Producer sends message
3. Kafka decides partition
4. Message appended sequentially
5. Offset assigned

Partition selection:

If key provided:

```
hash(key) % number_of_partitions
```

Same key → Same partition → Order preserved.

If no key → Round-robin distribution.

---

# 7. Consumer Flow

Consumers PULL data using poll().

Example:

```
while(true) {
    records = consumer.poll(Duration.ofMillis(100));
}
```

Kafka does NOT push data.

---

# 8. Consumer Groups

If multiple consumers share same group.id:

* Each partition is assigned to ONLY ONE consumer in that group.
* Kafka distributes partitions automatically.

Rule:

Maximum parallelism = Number of partitions

Example:
3 partitions
3 consumers (same group)
→ Each consumer gets 1 partition

If consumers > partitions → extra consumers idle

If consumers < partitions → some consumers handle multiple partitions

---

# 9. How Partition Assignment Works

When consumers start:

1. They join a group.
2. Kafka selects a Group Leader.
3. Leader runs partition assignment strategy.
4. Partitions distributed.

This process is called REBALANCING.

Default strategies:

* RangeAssignor
* RoundRobinAssignor
* StickyAssignor
* CooperativeStickyAssignor

Minimal configuration required:

```
props.put(ConsumerConfig.GROUP_ID_CONFIG, "order-group");
```

No manual partition mapping required normally.

---

# 10. Consumer and Threads

Important: KafkaConsumer is NOT thread-safe.

Patterns:

1. One Consumer Per Thread (Recommended)

Thread 1 → Consumer 1
Thread 2 → Consumer 2

2. Multiple Consumers in Same Group

Each consumer runs independently.
Kafka handles partition assignment.

3. One Consumer + Worker Thread Pool

Consumer polls records.
Records sent to executor service for parallel processing.

But offset commit must be handled carefully.

---

# 11. Example: Plain Java Consumer

```
Properties props = new Properties();
props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
props.put(ConsumerConfig.GROUP_ID_CONFIG, "order-group");
props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
consumer.subscribe(Collections.singletonList("orders"));

while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
    for (ConsumerRecord<String, String> record : records) {
        System.out.println("Partition: " + record.partition());
        System.out.println("Offset: " + record.offset());
        System.out.println("Value: " + record.value());
    }
}
```

---

# 12. Spring Boot Consumer Example

application.properties

```
spring.kafka.consumer.group-id=order-group
spring.kafka.listener.concurrency=3
```

Consumer:

```
@KafkaListener(topics = "orders", groupId = "order-group")
public void consume(String message) {
    System.out.println(message);
}
```

concurrency=3 → Spring creates 3 consumer threads.

---

# 13. What Happens If Consumer Goes Offline?

* Messages remain in Kafka.
* When consumer restarts → continues from last committed offset.

Unless retention period expires.

---

# 14. Retention Policy

Kafka deletes messages based on:

* Time (default)
* Size

Example:

```
log.retention.hours=168
```

Messages are deleted after retention period even if not consumed.

---

# 15. Retry and Failure Handling

Kafka does NOT handle business retries automatically.

Retries handled by:

* Consumer code
* Framework (e.g., Spring Kafka)

Patterns:

* Retry logic in code
* Retry Topic
* Dead Letter Topic (DLT)

---

# 16. Delivery Semantics

1. At Most Once
   Message may be lost but never duplicated.

2. At Least Once (Default)
   Message may be duplicated but never lost.

3. Exactly Once
   Requires idempotent producer and transactions.

---

# 17. Leader and Follower Partitions

Each partition has:

* 1 Leader
* Multiple Followers (based on replication factor)

Producer writes to Leader.
Followers replicate data.

If leader fails → follower becomes new leader.

---

# 18. Replication and ISR

Replication Factor determines number of copies.

ISR (In-Sync Replicas):
Followers fully caught up with leader.

Kafka ensures durability using replication.

---

# 19. Consumer Lag

Consumer lag =
Latest offset − Committed offset

High lag means consumer is slow.

---

# 20. Ordering Rules

✔ Ordered inside a partition
✖ Not ordered across partitions

To maintain per-user order:

```
kafkaTemplate.send("orders", userId, orderData);
```

Same key → Same partition

---

# 21. Important Production Concepts

* Rebalancing
* Consumer Lag Monitoring
* Backpressure handling
* Manual vs Auto offset commit
* Batch consumption
* Dead Letter Topics
* Partition strategy planning

---

# 22. Key Rules Summary

1. Kafka is a distributed log system.
2. Topic is divided into partitions.
3. Ordering is per partition.
4. Maximum parallelism = number of partitions.
5. Consumer group enables load balancing.
6. Offset tracks progress.
7. Retention policy controls data lifecycle.
8. Replication ensures durability.

---

# Final Definition

Kafka is a distributed, fault-tolerant, high-throughput event streaming platform that stores data in partitioned append-only logs and enables scalable real-time processing using consumer groups and replication mechanisms.

---


