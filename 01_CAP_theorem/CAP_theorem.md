# CAP Theorem – Complete Notes

## What is the CAP Theorem?
CAP Theorem (Brewer’s Theorem) is a fundamental principle in distributed systems describing a trade-off between:
- Consistency (C)
- Availability (A)
- Partition Tolerance (P)

It states: a distributed system can guarantee at most two of the three properties simultaneously. During network partitions, a choice must be made between Consistency and Availability. Proposed by Eric Brewer (2000) and formalized by Gilbert & Lynch (2002).

---

## The three components

1. Consistency (C)
- Every read returns the most recent write (or an error).
- All nodes see the same data at the same time.
- Pros: Reliable, predictable reads.
- Cons: Higher latency (synchronization), possible unavailability during partitions.
- Analogy: Everyone editing a single live Google Doc sees the same content.

2. Availability (A)
- Every request receives a response (success or failure).
- System continues to reply even if some nodes are down.
- Pros: High uptime, low latency.
- Cons: May return stale/outdated data.
- Analogy: A restaurant chain where another branch serves you if one is closed.

3. Partition Tolerance (P)
- System keeps working despite network failures between nodes.
- Pros: Fault-tolerant, resilient.
- Cons: Increased system complexity and harder to keep strong consistency.

---

## Key trade-off
- Network partitions are inevitable in distributed systems → Partition Tolerance is mandatory.
- When a partition occurs, you must choose:
  - C (Consistency) — sacrifice availability, or
  - A (Availability) — sacrifice consistency.
- Thus, practical systems are generally CP or AP. CA is only applicable when partitions do not occur (single-node or fully connected environments).

Table of common categories:
- CA (Consistency + Availability): works only with no partitions (e.g., single-node RDBMS).
- CP (Consistency + Partition Tolerance): stays consistent, can reject requests during partitions (e.g., MongoDB in some configs, HBase).
- AP (Availability + Partition Tolerance): stays available, may return stale data (e.g., Cassandra, DynamoDB, CouchDB).

```
ASCII visualization:
       Consistency
        /       \
       /         \
      /           \
 Availability --- Partition Tolerance
 ```

Pick any two, not all three at once.

---

## CAP Theorem for Databases and Partition Tolerance
- Under normal operation, a distributed database can appear to provide C, A, and P.
- During network partitions, P is required; the system must choose C or A.
- CP databases prioritize correctness (may become unavailable during partition).
- AP databases prioritize uptime (may serve eventual/stale data).

Examples:
- CP: MongoDB (config-dependent), HBase, Zookeeper (focus on consistency)
- AP: Cassandra, DynamoDB, CouchDB (focus on availability/eventual consistency)

---

## Eventual consistency
- Common in AP systems: replicas converge to the same value eventually.
- Reads may reflect stale data until the system heals and propagation completes.

---

## CAP vs ACID — Short comparison
- CAP applies to distributed systems (trade-offs during partitions).
- ACID applies to transactions (single-node DBs) to ensure correctness and durability.

ACID components:
- Atomicity: all-or-nothing transactions.
- Consistency: transaction moves DB from valid state to valid state (constraints maintained).
- Isolation: concurrent transactions do not interfere.
- Durability: committed transactions persist after crashes.

Difference in "Consistency":
- CAP Consistency: all replicas see the same most recent data.
- ACID Consistency: each transaction preserves database integrity rules.

When to use:
- Banking/financial systems → ACID (strong correctness).
- Large-scale distributed services → CAP considerations (pick CP or AP based on requirements).

---

## Real-world examples (brief)
- MongoDB → often CP (can reject writes to keep strong consistency)
- Cassandra → AP (prioritizes availability and partition tolerance)
- DynamoDB → AP (eventual consistency modes)
- Zookeeper → CP (coordination service prioritizing consistency)

---

## Decision guidance
- If correctness is critical (e.g., banking), prefer consistency (CP).
- If uptime and responsiveness matter more than immediate correctness (e.g., social feeds), prefer availability (AP).
- Design choices also include hybrid strategies: tunable consistency, quorum reads/writes, conflict resolution (CRDTs), and multi-master replication schemes.

---

## Interview tip
“In the presence of a network partition, a distributed system must choose between Consistency and Availability. Partition Tolerance is a practical necessity.”

---

## Quick summary table
Property | Description | Benefit | Trade-off
---|---:|---|---
Consistency (CAP) | All nodes see same data | Reliable reads | Lower availability during partitions
Availability | Every request gets a response | High uptime | May serve stale data
Partition Tolerance | System runs across network failures | Fault tolerant | Harder to maintain strict consistency

---

## Final notes
- Partition failures happen; plan for them.
- Use quorum techniques, read/write tuning, and application-level conflict handling to balance needs.
- CAP is a guideline for trade-offs, not a strict prohibition — many systems implement configurable compromises (e.g., tunable consistency levels).

---

## Modern CAP perspective — SQL vs NoSQL (choosing C vs A)

> “Choosing consistency and availability comes when choosing which database type to go with, such as SQL vs NoSQL. NoSQL databases can be classified based on whether they support high availability or high consistency.”

What this means (concise)
- Partition Tolerance (P) is effectively mandatory in distributed systems. When partitions occur you must choose to favor:
  - Consistency (C): guarantee latest-correct data (may reduce availability), or
  - Availability (A): always respond (may return stale data).
- Database choice often reflects that preference:
  - SQL (traditional RDBMS): typically favors strong consistency and ACID semantics.
  - NoSQL (distributed systems): often designed for availability and scale; some NoSQL engines favor consistency (CP), others favor availability (AP).

NoSQL classification (simple)
- CP (Consistency + Partition tolerance): prioritizes correctness during partitions; may reject or delay operations. Examples: HBase, some MongoDB configurations, Zookeeper.
- AP (Availability + Partition tolerance): prioritizes responsiveness; serves reads/writes even under partition; uses eventual consistency. Examples: Cassandra, DynamoDB, CouchDB.

Practical techniques to balance C and A
- Tunable consistency: many systems let you choose consistency levels per operation (e.g., strong, bounded staleness, eventual).
- Quorum reads/writes: configure R (reads) and W (writes) and quorum N such that R + W > N for stronger consistency.
- Read-your-writes and session guarantees: provide better UX without full global consistency.
- Conflict resolution: last-write-wins, vector clocks, or CRDTs for automatic merge during recovery.
- Multi-region strategies: active-passive vs active-active, with trade-offs in latency and conflict handling.

Recovery and operation during partitions
- Plan for degraded modes (read-only, stale-reads allowed, local queues for writes).
- Use anti-entropy, read repair, and background reconciliation after partitions heal.
- Monitor latency, error rates, and write conflicts; alert on partition events.

Design patterns and examples
- Banking (strong correctness): choose systems/configs that prioritize consistency (CP patterns, SQL + distributed transactions or strongly consistent NoSQL configs).
- Social feed or cached content (high availability): choose AP systems and accept eventual consistency; use background reconciliation.
- Hybrid: use a strongly-consistent core for critical data and an eventually-consistent layer for scalable reads/feeds.

Decision checklist (quick)
- Is correctness at all times required? → Choose strong consistency (CP/ACID).
- Is uptime responsiveness more important than perfect immediate accuracy? → Choose availability (AP eventual consistency).
- Need both in parts of the system? → Consider hybrid architecture, tunable consistency, or separation of concerns.

Summary (one-liner)
- The modern CAP goal is to maximize the useful combination of consistency and availability for your application: select a database and configuration that match your correctness, latency, and operational recovery requirements, and plan explicitly for behavior during and after partitions.

---


# 📡 Consistency Models in Distributed Systems

## Distributed databases use different consistency models (ways to define what “consistent” means):


| Model                           | Description                                             | Example                          |
| ------------------------------- | ------------------------------------------------------- | -------------------------------- |
| **Strong Consistency**          | Every read gets the latest write                        | CP systems like MongoDB          |
| **Weak Consistency**            | Updates are propagated eventually, no guarantee on time | AP systems like DynamoDB         |
| **Eventual Consistency**        | All replicas become consistent *eventually*             | Cassandra                        |
| **Causal Consistency**          | Preserves cause-effect relationships between operations | Some distributed caching systems |
| **Read-your-write Consistency** | A client always reads its own writes                    | DynamoDB session consistency     |


--- 


## Practical examples — When to prioritize Consistency vs Availability

Financial / consistency-first examples
- Financial data requires exact correctness. Users must always see accurate balances and transaction history; an erroneous or missing value is unacceptable.
- Real-world examples that typically require strong consistency:
  - Bank account balances
  - Text messages (ordered delivery / correctness)
  - Inventory (to avoid overselling)
  - Airline reservation systems
  - Payrolls
  - Student records
  - Health records
  - Energy management systems

Databases commonly used when prioritizing consistency (examples)
- MongoDB (config-dependent strong modes)
- Redis (when used with strong persistence/consensus setups)
- HBase

Availability-first examples
- Availability-first systems are chosen when service continuity and responsiveness are more important than immediate absolute correctness.
- Example: E-commerce storefronts and shopping carts — stores want the site and cart functionality available 24/7 even if some data is eventually consistent.

Databases commonly used when prioritizing availability (examples)
- Cassandra
- DynamoDB
- Cosmos DB

Tunable consistency and hybrid approaches
- Many modern databases (e.g., Cosmos DB, Cassandra, DynamoDB) expose tunable consistency levels so you can select per-workload guarantees (strong, bounded staleness, session, eventual).
- Use hybrid architectures: critical data on strongly-consistent stores, and scalable reads/feeds on eventually-consistent layers.
- Design for degraded modes (read-only, queue writes locally) and automated recovery (anti-entropy, read-repair) after partitions.

Summary
- Choose consistency-first databases for correctness-critical domains (finance, health, reservations).
- Choose availability-first databases for latency/uptime-critical services (large-scale web apps, feeds).
- When possible, tune consistency per operation or combine layers to get the best of both worlds.

