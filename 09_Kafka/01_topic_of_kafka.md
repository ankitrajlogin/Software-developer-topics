# Apache Kafka – Core Concepts (Beginner Notes)

This document explains the fundamental building blocks of Kafka. Understanding these concepts clearly is extremely important because everything in Kafka is built on top of them.

---

# 1. Producer

## What is a Producer?

A **Producer** is an application that sends data to Kafka.

* Data is called a **Message** or **Message Record**
* For Kafka, a message is simply a **byte array**
* Kafka does not care about structure (JSON, CSV, XML, etc.)

## Examples

| Use Case         | What is Sent as Message? |
| ---------------- | ------------------------ |
| Text file upload | Each line of file        |
| Database table   | Each row                 |
| SQL query result | Each result row          |
| Smart meter      | Load data per minute     |

If you want to send data into Kafka, you must build or use a Producer application.

---

# 2. Consumer

## What is a Consumer?

A **Consumer** is an application that reads data from Kafka.

Important:

* Producers do NOT send data directly to consumers.
* Producers send data to Kafka server.
* Consumers request data from Kafka.

## How Consumer Works

1. Consumer requests messages.
2. Broker sends available messages.
3. Consumer processes them.
4. Consumer requests more.
5. Loop continues.

This continues as long as new data is arriving.

---

# 3. Broker

## What is a Broker?

A **Broker** is simply the Kafka server.

It acts as a message middleman between Producer and Consumer.

Producer → Broker → Consumer

There is:

* No direct communication between producer and consumer
* All communication happens via broker

---

# 4. Cluster

## What is a Kafka Cluster?

Kafka is a distributed system.

A **Cluster** is a group of computers running Kafka brokers together.

Each machine runs one broker instance.

Why cluster?

* High availability
* Scalability
* Fault tolerance
* Distributed storage

---

# 5. Topic

## What is a Topic?

A **Topic** is a unique name given to a stream of data.

You can think of it like:

* A database table
* A logical category of messages

## Why Topic is Needed?

Without topic, broker would not know:

* Which producer’s data?
* Which type of data?

Topics remove confusion and organize data.

## Example (Smart Meter)

We can design three topics:

* Current-Load-Topic
* Consumed-Units-Topic
* Input-Fluctuations-Topic

Producers send data to specific topics.
Consumers request data by topic name.

Important:

* Topic creation is a design-time decision.
* Architects decide how many topics are needed.

---

# 6. Partitions

## Why Partitions?

Topics can become very large.

Example:

* 100,000 smart meters
* Sending data every minute
* Terabytes of data quickly

A single machine cannot handle this.

## What is a Partition?

A **Partition** is a smaller, independent portion of a topic.

Each partition:

* Lives on one machine
* Is the smallest storage unit
* Cannot be broken further

## Why Partitions Are Important

Partitions solve:

1. Storage scalability
2. Processing scalability
3. Parallelism

Partitions are the core concept behind Kafka scalability.

## Who Decides Number of Partitions?

Kafka does NOT decide.

It is a design decision made when creating the topic.

You must estimate properly because partitions cannot be split later.

---

# 7. Offset

## What is an Offset?

Offset is a unique sequence number of a message inside a partition.

## How Offset Works

* First message → Offset 0
* Next → 1
* Next → 2
* And so on...

Important:

* Offsets are immutable
* Ordering is maintained within partition
* No global ordering across partitions

## To Locate a Message You Need:

1. Topic name
2. Partition number
3. Offset number

---

# 8. Consumer Group

## What is a Consumer Group?

A **Consumer Group** is a group of consumers working together to share workload.

## Why Consumer Group?

If data volume is huge, one consumer cannot handle it.

Solution:

* Start multiple consumers
* Put them in same group
* Divide partitions among them

## Partition Assignment Rule

One partition can be consumed by only ONE consumer in a group.

Example:

* 500 partitions
* 100 consumers

Each consumer handles 5 partitions.

Maximum parallel consumers = Number of partitions.

You cannot exceed partition count within a group.

---

# Complete Example – Retail Chain

## Scenario:

* Hundreds of stores
* Many billing counters
* Each counter sends invoice data

## Step 1 – Producers

Each billing counter acts as a producer and sends invoices to Kafka topic.

## Step 2 – Topic & Partitions

Create topic: Invoices-Topic

Partition it across cluster to distribute storage and workload.

## Step 3 – Kafka Cluster

Multiple brokers store partitions across machines.

## Step 4 – Consumer Group

At data center:

* Start multiple consumers
* Put them in one group
* Each reads different partitions
* All invoices processed in parallel

---

# Critical Learning Points

| Concept        | Why Important       |
| -------------- | ------------------- |
| Producer       | Sends data          |
| Consumer       | Reads data          |
| Broker         | Middleman           |
| Cluster        | Distributed system  |
| Topic          | Logical data stream |
| Partition      | Scalability unit    |
| Offset         | Message order       |
| Consumer Group | Parallel processing |

---

# Final Understanding

Kafka architecture:

Producers → Topics (Partitions) → Broker Cluster → Consumer Groups

Kafka provides:

* Decoupling
* Scalability
* Fault tolerance
* High throughput
* Real-time processing
