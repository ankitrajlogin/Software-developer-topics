# System Design: Scalability — Complete Notes

## What is Scalability?
Scalability is the ability of a system to handle increased load (users, traffic, data) by adding resources without degrading performance. A scalable system maintains acceptable latency, throughput, and reliability as demand grows.

Example: an app that works for 100 users and, after adding resources or architecture changes, still works smoothly for 100k users is scalable.

---

## Why Scalability Matters
- Growth Handling: supports more users and data as business grows.
- Performance Stability: keeps latency low and throughput high.
- Cost Efficiency: scale resources according to demand (pay-as-you-grow).
- Reliability & Fault Tolerance: scalable systems often reduce single points of failure.
- Global Reach: serve users from multiple regions with acceptable latency.

---

## Types of Scalability
1. Vertical Scaling (Scale Up)
- Increase capacity of a single machine (CPU, RAM, disk).
- Pros: simple, minimal architectural changes.
- Cons: finite limit, expensive high-end hardware, potential downtime.

2. Horizontal Scaling (Scale Out)
- Add more machines/nodes and distribute load.
- Pros: virtually unlimited, fault-tolerant, cost-effective at scale.
- Cons: increased system complexity (distributed state, consistency).

Table — Vertical vs Horizontal
| Feature | Vertical (Scale Up) | Horizontal (Scale Out) |
|---|---:|---|
| How it scales | Bigger machine | More machines |
| Complexity | Low | High |
| Limit | Hardware bound | Near unlimited |
| Fault tolerance | Lower | Higher |
| Cost profile | Expensive hardware | Commodity servers |

---

## Dimensions of Scalability
- Load Scalability: handle more requests.
- Data Scalability: store and retrieve larger datasets.
- Geographic Scalability: serve users across regions.
- Administrative Scalability: allow multiple teams to operate the system.
- Functional Scalability: add features without breaking existing ones.

---

## Key Metrics and Targets
- Latency (p95, p99) — response time percentiles
- Throughput (requests/sec)
- Error rate (4xx/5xx)
- Resource utilization (CPU, memory, I/O)
- Capacity (concurrent users, requests per second)
- Cost per request / cost per user

Concrete targets: p99 latency < 500ms; availability 99.95% (SLA) — tailor to product needs.

---

## Common Scalability Patterns and Techniques
1. Load Balancing
- Distribute requests across multiple servers.
- Types: round-robin, least-connections, IP-hash.
- Use health checks and sticky sessions only if necessary.

2. Caching
- Reduce load and latency by storing frequently used data.
- Layers: client, CDN, reverse proxy (Varnish), application cache (Redis, Memcached), DB query cache.
- Invalidation strategies: TTL, write-through, write-back, cache-aside.

3. Database Scaling
- Read Replicas: scale read-heavy workloads.
- Sharding (Horizontal Partitioning): split data by key across nodes for write & storage scale.
- Partitioning: range, hash, or directory-based.
- Replication: synchronous (strong consistency) or asynchronous (higher availability).

4. Asynchronous Processing
- Offload long-running tasks to background workers (message queues: Kafka, RabbitMQ, SQS).
- Improves request latency and overall throughput.

5. CQRS (Command Query Responsibility Segregation)
- Separate write model (commands) from read model (queries); tailor scalability of each.

6. Event Sourcing & Streams
- Use append-only logs (Kafka) to decouple producers and consumers; good for replay and recovery.

7. Rate Limiting & Throttling
- Protect services from overload; apply per-user, per-IP, or per-API-key limits.

8. Autoscaling
- Dynamically adjust instance counts based on metrics (CPU, requests/sec, custom metrics).
- Consider warm-up times, cooldown windows, and scaling granularity.

9. CDN & Edge Caching
- Offload static content and cache dynamic pages closer to users to reduce latency.

10. Compression & Protocol Optimization
- Use compression, HTTP/2, gRPC, and connection pooling to reduce overhead.

---

## Design Considerations & Trade-offs
- Consistency vs Availability (CAP): distributed state requires trade-offs.
- Latency vs Throughput: batching increases throughput but adds latency.
- Complexity vs Simplicity: distributed solutions increase operational burden.
- Cost vs Performance: over-provisioning increases cost; aggressive autoscaling can help.

---

## Scalability Testing
- Load Testing: simulate target throughput (tools: JMeter, Gatling).
- Stress Testing: push beyond limits to find breaking points.
- Soak Testing: run for prolonged periods to discover resource leaks.
- Chaos Testing: inject failures to validate resilience (Chaos Monkey).
- Capacity Planning: determine required resources for peak loads.

---

## Monitoring & Observability
- Metrics (Prometheus): latency, throughput, errors, resource usage.
- Tracing (Jaeger, Zipkin): distributed request paths, latency hotspots.
- Logging (ELK/EFK): structured logs, aggregation.
- Alerts: thresholds for error rates, high latency, high CPU, unusual traffic.

---

## Example Architectures (short)
1. Simple Read-Heavy App
- Web tier + LB → app servers → DB primary + read replicas → Redis cache → CDN
2. High Write / Stream Processing
- API → LB → app → write to append-only log (Kafka) → workers process events → materialized views for queries.
3. Global App
- Multiple regions with active-active services, geo-DNS or global LB, data replication with conflict resolution, edge caching.

ASCII graph — simplified:
    Clients
      |
    CDN / LB
      |
   App Pool -- Cache (Redis)
      |
   Queue (Kafka) --> Workers
      |
   DB Shards / Replicas

---

## Practical Tips & Checklist
- Define SLAs (latency, availability) before design.
- Start simple: vertical scaling + caching, then introduce horizontal patterns as needed.
- Profile and identify bottlenecks (DB, CPU-bound, I/O).
- Cache closest to user where possible (CDN, browser).
- Use read replicas for read-heavy traffic; shard for write-heavy and large datasets.
- Prefer idempotent APIs for retries and safe scaling.
- Plan for partition recovery: reconciliation, conflict resolution, and anti-entropy.
- Automate deployment and scaling (IaC, CI/CD) to reduce human error.
- Implement circuit breakers and bulkheads to contain failures.
- Test autoscaling and failure scenarios in staging.

---

## Glossary (short)
- Sharding: splitting dataset across multiple nodes.
- Replication: copying data across nodes for redundancy/read scale.
- Quorum: majority-based read/write guarantee for consistency.
- Anti-entropy: background reconciliation to converge replicas.
- Bulkhead: isolate components to prevent cascading failures.

---

## Interview Tip (one-liner)
Explain trade-offs clearly: identify bottlenecks, pick patterns (cache, LB, sharding) and justify choices with metrics and operational concerns.

---



## Sharding vs Partitioning

Are they the same thing?  
They are closely related but not exactly the same. Both divide large data into smaller parts to improve performance and scalability, but differ in scope, goals, and deployment.

### What is Partitioning?
- Concept: Dividing a large database or table into smaller, more manageable parts called partitions. All partitions belong to the same logical database.
- How it works: split data by range, list, hash, or composite rules (e.g., Orders by quarter).

Example partition layout:
Partition | Data range
---|---
P1 | Orders Jan–Mar
P2 | Orders Apr–Jun
P3 | Orders Jul–Sep
P4 | Orders Oct–Dec

Types of partitioning:
- Range partitioning (e.g., date or ID ranges)
- List partitioning (specific values such as region)
- Hash partitioning (hash(key) % N for even distribution)
- Composite partitioning (combination of methods)

Advantages:
- Better query performance (scan smaller subsets)
- Easier backups and maintenance
- Efficient management of large datasets

Disadvantages:
- More complex schema management
- Uneven partitions can cause hotspots
- Usually within a single server (not horizontally distributed by default)

---

### What is Sharding?
- Concept: A form of horizontal partitioning across multiple servers or database instances. Each shard is an independent database holding a subset of the total data.
- How it works: choose a shard key (e.g., user_id), apply a strategy (range, hash, directory), and route requests to the correct shard. Optionally add replication for fault tolerance.

Example shard layout:
Shard | User range | Server
---|---:|---
Shard 1 | user_id 1–10,000 | Server A
Shard 2 | user_id 10,001–20,000 | Server B
Shard 3 | user_id 20,001–30,000 | Server C

Advantages:
- True horizontal scalability (add shards/servers)
- Better performance per server (smaller dataset)
- Fault isolation (one shard failure doesn't affect others)

Disadvantages:
- Complex to implement and maintain
- Cross-shard joins and transactions are hard
- Re-sharding (rebalancing) can be difficult
- Requires routing logic or middleware

Examples: MongoDB, Cassandra, DynamoDB support sharding/partitioning; Twitter shards user data across DBs.

---

### Key differences

| Aspect | Partitioning | Sharding |
|---|---:|---|
| Definition | Divide data within a single database | Distribute data across multiple DB instances/servers |
| Scope | Usually within one machine | Across multiple machines (distributed) |
| Goal | Manageability & performance | Scalability & distribution |
| Location | Same server / DB engine | Different servers or clusters |
| Access | Single DB engine manages partitions | Each shard is an independent DB instance |
| Joins/Transactions | Easier (same DB) | Harder (across shards) |
| Example | MySQL partitioned by date | Twitter user data split across shards |

---

### Analogy
- Partitioning: one big library organized into sections (same building).
- Sharding: multiple library branches, each with part of the collection (separate buildings).

---

### How they work together
You can combine both: shard data across servers and partition data inside each shard.

Example:
- Shard 1 (Server A): Users 1–1M → partitioned by month (Jan, Feb, ...)
- Shard 2 (Server B): Users 1M–2M → partitioned by month

Hybrid benefits:
- Horizontal scalability via sharding
- Manageability and performance via partitioning

---

### Real-world examples
Company | What they use | Explanation
---|---|---
Facebook | Sharding | User data split by ID ranges across many MySQL shards
Amazon DynamoDB | Partitioning + Sharding | Hash-based partitioning distributed across nodes automatically
YouTube | Sharding | Videos and metadata stored across shards by category/region
PostgreSQL / MySQL | Partitioning | Range or hash partitions within a single instance

---

### Final summary

| Feature | Partitioning | Sharding |
|---|---:|---|
| Purpose | Split large table/data for performance | Split database across servers for scalability |
| Scope | Within single database | Across multiple databases/servers |
| Used for | Maintenance, query optimization | Scaling large systems horizontally |
| Data location | Same machine | Multiple machines |
| Relation | Local organization inside DB | Sharding = horizontal partitioning across systems |
| Best for | Moderate-scale systems | Internet-scale systems |

Quick memory tip:  
Partitioning = organize data within a system.  
Sharding = distribute data across systems.

---

## Example Scenario: Social Media Application (ChatConnect)

### Initial state
- Users: 1,000  
- Posts/day: 100  
- Deployment: single small server (Node.js backend + MySQL)

### Problem after growth
- Users: 5,000,000  
- Posts/day: 1,000,000  
- Symptoms: slow loading, timeouts, DB overload → need scalability.

### Step-by-step scaling

1️⃣ Vertical Scaling (Scale Up)  
- Action: upgrade server (CPU 4 → 32 cores, RAM 8GB → 128GB, move to SSD).  
- Result: temporary performance boost; limited by hardware ceiling.

2️⃣ Horizontal Scaling (Scale Out)  
- Action: deploy multiple backend servers + load balancer (Nginx/ELB).  
- Result: distribute requests across servers for improved throughput and availability.

Architecture sketch:
- Load Balancer → multiple Web/App servers (stateless) → shared datastore

3️⃣ Database Scaling
(a) Read Replicas  
- Add read-only replicas; reads hit replicas, writes go to master.  
(b) Sharding (horizontal partitioning)  
- Split user/post data across DB instances by key ranges or hash.  
(c) Vertical partitioning  
- Split tables by responsibilities (e.g., UserCore vs UserProfile).

4️⃣ Caching  
- Use Redis/Memcached for hot data (trending posts, user profiles).  
- Effect: reduce DB load, e.g., response time 200ms → 20ms for cached queries.

5️⃣ Load Balancing  
- Ensure LB health checks and session handling (avoid sticky sessions if possible).

6️⃣ CDN for Static Assets  
- Offload images/videos to CDN (CloudFront, Cloudflare) for regional edge delivery.

7️⃣ Asynchronous Processing  
- Use message queues (Kafka/RabbitMQ/SQS) for background tasks: image processing, notifications.

8️⃣ Microservices  
- Split monolith into services: Auth, Posts, Media, Notifications. Scale services independently.

9️⃣ Auto-scaling  
- Configure autoscaling rules (CPU, requests/sec, custom metrics) with warm-up and cooldown windows.

🔟 Multi-Region Deployment  
- Deploy in multiple regions (e.g., US, EU, APAC) with geo-DNS or global LB and data replication/failover.

### Final Scalable Architecture (summary)
- Load Balancer → App Servers (microservices)  
- Cache layer (Redis)  
- Database layer: sharded DBs + read replicas + vertical partitioning  
- CDN + Object Storage (S3) for media  
- Message Queue (Kafka) for async jobs  
- Multi-region deployments with global routing



### Key takeaways
- Start simple: vertical scaling + caching, then introduce horizontal patterns.  
- Use read replicas and caching for read-heavy loads; shard for write/size limits.  
- Adopt async processing and microservices to decouple workloads.  
- Enable autoscaling and multi-region deployments for global availability and cost efficiency.  
- Plan for recovery and reconcilation (anti-entropy, read-repair) after partitions.

### Example summary table (ChatConnect)

| Stage | Solution | Benefit |
|---:|---|---|
| 1 | Vertical scaling | Temporary speed boost |
| 2 | Horizontal scaling + LB | Handle more users |
| 3 | Sharding + Replicas | Prevent DB bottleneck |
| 4 | Caching | Faster responses |
| 5 | CDN | Faster asset delivery |
| 6 | Microservices | Independent scaling |
| 7 | Auto-scaling | Adaptive capacity |
| 8 | Multi-region | Global low-latency service

---
