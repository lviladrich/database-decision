---
name: database-decision
description: >
  Guide developers through choosing the right database technology BEFORE writing any code.
  Covers SQL (PostgreSQL, MySQL), NoSQL document (MongoDB), graph (Neo4j), key-value (Redis),
  wide-column (Cassandra), object-oriented (InterSystems IRIS), time-series (InfluxDB), and
  multidimensional databases. Provides a structured decision framework based on data shape,
  consistency needs, scalability requirements, CAP theorem trade-offs, and business constraints.

  ALWAYS use this skill when the user:
  - Asks "which database should I use" or "what DB for my project"
  - Starts building an app/system/API without mentioning a database choice
  - Mentions choosing between SQL and NoSQL
  - Asks about MongoDB vs PostgreSQL or any database comparison
  - Is designing a system architecture with data storage needs
  - Mentions "polyglot persistence" or "multi-model" or "persistence layer"
  - Asks about CAP theorem, ACID vs BASE, or consistency trade-offs
  - Says "I need a database for..." followed by any use case
  - Is building e-commerce, IoT, social network, analytics, real-time, or recommendation systems
  - Mentions sharding, replication, scaling, or distributed databases
  - Asks about graph databases, time-series, key-value stores, or columnar databases
  - Is working on a university project involving database selection or justification
  - Uses phrases like "store data", "persist data", "data layer", "backend database"

  Even if the user already picked a database, STOP and validate that choice before helping them implement it.
  The wrong database is the most expensive technical debt — it's easier to rewrite code than to migrate data.
---

# Database Decision Framework

## CRITICAL RULE: DECIDE BEFORE YOU CODE

**STOP. Before writing a single line of database code, schema, or query — the developer MUST justify their database choice.**

An AI agent can generate perfect MongoDB queries or flawless SQL schemas. But if the technology is wrong for the use case, perfect code on the wrong foundation is technical debt from day one.

This skill enforces a **decide-first, code-second** approach.

---

## STEP 1: REQUIREMENTS EXTRACTION

Before recommending any database, gather these inputs. Ask the user if not provided:

### Data Shape Questions
- What does the data look like? (structured/semi-structured/unstructured/relationships/time-indexed)
- Are attributes fixed or variable across records?
- How deep are relationships between entities? (flat, 1-2 levels, deep hierarchies, networks)
- What is the read-to-write ratio? (read-heavy, write-heavy, balanced)

### Scale Questions
- Expected data volume? (MBs, GBs, TBs, PBs)
- Expected concurrent users? (tens, hundreds, thousands, millions)
- Geographic distribution? (single region, multi-region, global)
- Growth trajectory? (stable, linear, exponential)

### Consistency & Availability Questions
- Can the system tolerate stale reads? (eventual consistency acceptable?)
- Are there financial transactions or operations requiring strict ACID guarantees?
- What is the acceptable downtime? (99.9%, 99.99%, zero tolerance)
- Does every component need the same consistency level?

### Business Constraints
- Team expertise? (SQL-proficient, NoSQL experience, mixed)
- Budget? (open-source preferred, managed services OK, enterprise licensing acceptable)
- Existing infrastructure? (on-premise, cloud provider, Docker/Kubernetes)
- Time to market pressure?

---

## STEP 2: DATABASE SELECTION MATRIX

### When to Use Each Database Type

#### Relational (PostgreSQL, MySQL) → STRUCTURED DATA + STRONG CONSISTENCY
**Choose when:**
- Data has a well-defined, stable schema
- ACID transactions are mandatory (financial systems, user accounts, orders)
- Complex queries with JOINs across multiple entities
- Referential integrity is critical
- Data volume fits on a single server or moderate clustering

**CAP position:** CA — Consistency + Availability (sacrifices Partition tolerance)

**Real-world signals:** "users table", "order processing", "bank transactions", "inventory management", "login system", "billing", "ERP"

**AVOID when:** Schema changes frequently, data is hierarchical/nested, need horizontal scaling to hundreds of nodes, or handling millions of writes/second.

---

#### Document Store (MongoDB) → FLEXIBLE SCHEMA + HIERARCHICAL DATA
**Choose when:**
- Attributes vary across records (product catalogs with different fields per category)
- Data is naturally hierarchical or nested (JSON-like structures)
- Schema evolves frequently as the product iterates
- Need horizontal scaling with built-in sharding
- Semi-structured data: logs, user profiles, content management

**CAP position:** CP — Consistency + Partition tolerance (sacrifices Availability during partition)

**Sharding strategies:** By range (dates, IDs), by hash (uniform distribution), geographic (latency optimization)

**Real-world signals:** "product catalog", "user profiles", "CMS", "blog posts", "flexible attributes", "different fields per record", "JSON documents"

**AVOID when:** Data is highly relational with many JOINs, require strict multi-document ACID (though MongoDB 4.0+ supports multi-doc transactions), or data is simple key-value pairs.

---

#### Key-Value Store (Redis) → SPEED + SIMPLE ACCESS PATTERNS
**Choose when:**
- Need sub-millisecond latency
- Access pattern is simple: get/set by key
- Caching layer for another database
- Session management, shopping carts, real-time counters
- Pub/sub messaging, leaderboards, rate limiting
- Data fits in memory (or acceptable with eviction policies)

**CAP position:** CP — Consistency + Partition tolerance (in cluster mode)

**Data structures:** Strings, Lists, Sets, Hashes, Sorted Sets, Streams

**Real-world signals:** "cache", "sessions", "real-time", "leaderboard", "counter", "queue", "rate limiter", "shopping cart", "temporary data"

**AVOID when:** Data exceeds available RAM significantly, need complex queries or aggregations, require persistent-first storage, or need relationships between data.

---

#### Graph Database (Neo4j) → RELATIONSHIP-CENTRIC DATA
**Choose when:**
- Relationships between entities ARE the primary value
- Need recursive or path-finding queries (shortest path, recommendations)
- Social networks, fraud detection, knowledge graphs, dependency trees
- Data naturally forms a network with many-to-many relationships
- Queries like "friends of friends who bought X" or "all paths between A and B"

**CAP position:** CA — Consistency + Availability (limited partition tolerance, primarily vertical scaling)

**Clustering:** Core servers (Raft protocol, M = 2F + 1 for fault tolerance) + Read Replicas (horizontal read scaling). Causal consistency guaranteed.

**Real-world signals:** "relationships", "connections", "recommendations", "fraud detection", "social network", "knowledge graph", "path finding", "who knows whom", "dependencies", "hierarchy traversal"

**AVOID when:** Need massive write throughput across distributed nodes, data is tabular without meaningful relationships, need to update millions of nodes at once, or data volume requires extreme horizontal distribution.

---

#### Wide-Column Store (Cassandra) → MASSIVE WRITE THROUGHPUT + GLOBAL DISTRIBUTION
**Choose when:**
- Write-heavy workloads (IoT sensors, event logging, metrics)
- Need multi-datacenter/global replication with no single point of failure
- Predictable, known query patterns (design tables around queries, not entities)
- Linear horizontal scalability (add nodes = proportional capacity increase)
- High availability is non-negotiable (AP system)
- Time-series-like data partitioned by time ranges

**CAP position:** AP — Availability + Partition tolerance (sacrifices strong Consistency, offers tunable consistency with N/R/W parameters)

**Consistency tuning with N, R, W:**
- Strong consistency: R + W > N (e.g., N=3, R=2, W=2)
- Fast writes: W=1, R=N
- Fast reads: W=N, R=1
- Quorum: W > N/2

**Real-world signals:** "IoT", "sensor data", "event log", "metrics", "time-series at scale", "multi-region", "always available", "millions of writes per second", "no downtime"

**AVOID when:** Need ad-hoc queries (Cassandra requires designing tables per query), need JOINs or complex aggregations, data model changes frequently, or read patterns are unpredictable.

---

#### Object Database (InterSystems IRIS) → COMPLEX OBJECT PERSISTENCE
**Choose when:**
- Application model has deep object hierarchies with inheritance and polymorphism
- Want to eliminate object-relational impedance mismatch (no ORM needed)
- Domain objects are complex with nested structures and behaviors
- Need both SQL and object navigation on the same data
- Healthcare, financial instruments, manufacturing systems with complex entities

**Real-world signals:** "complex objects", "inheritance hierarchies", "medical records", "financial instruments", "no ORM", "persistent objects", "multi-model platform"

**AVOID when:** Simple data structures, team has no OOP background, need maximum horizontal scalability, or open-source is a hard requirement.

---

#### Time-Series / Multidimensional (InfluxDB) → TIME-INDEXED DATA + ANALYTICS
**Choose when:**
- Data is primarily indexed by time (monitoring, metrics, IoT)
- Need specialized temporal operations: downsampling, retention policies, windowed aggregations
- High ingestion rate with automatic data lifecycle management
- Scientific data with multiple dimensions (geospatial, experimental)
- Compression is critical (90%+ reduction with specialized algorithms)

**Indexing:** R-trees, KD-trees, LSM-trees for multidimensional queries

**Real-world signals:** "metrics", "monitoring", "dashboards", "Grafana", "sensor data over time", "downsampling", "retention policies", "scientific experiments", "geospatial data"

**AVOID when:** Data is not time-indexed, need complex relationships, require general-purpose CRUD operations, or need transaction support.

---

## STEP 3: POLYGLOT PERSISTENCE — WHEN ONE DATABASE IS NOT ENOUGH

Most real-world systems need **multiple databases**, each optimized for a specific component. This is called **polyglot persistence** or **persistence polyglot architecture**.

### Decision Process for Multi-Database Systems

For each component of the system, independently ask:
1. What is the data shape for THIS component?
2. What are the access patterns for THIS component?
3. What consistency level does THIS component need?
4. What is the performance requirement for THIS component?

### Classic E-Commerce Example (Polyglot)

| Component | Database | Why |
|---|---|---|
| Users & Auth | PostgreSQL | ACID transactions, strong consistency, stable schema |
| Product Catalog | MongoDB | Variable attributes per category, flexible schema |
| Shopping Cart | Redis | Sub-ms latency, temporary data, high concurrency |
| Recommendations | Neo4j | "users who bought X also bought Y" = graph traversal |
| Search | Elasticsearch/MongoDB | Full-text search with dynamic filters |
| Order History & Analytics | Cassandra | High write volume, time-partitioned, multi-region |
| System Metrics | InfluxDB | Time-indexed monitoring, downsampling, dashboards |

### ACID vs BASE by Component

Not every component needs the same guarantees:

- **ACID required:** User accounts, financial transactions, orders, payments, inventory counts
- **BASE acceptable:** Recommendations, caches, session data, analytics, activity feeds, search indexes

---

## STEP 4: JUSTIFICATION TEMPLATE

ALWAYS produce a justification for the database choice. Use this structure:

```
COMPONENT: [Name]
CHOSEN DATABASE: [Technology]
DATABASE TYPE: [Relational / Document / Key-Value / Graph / Wide-Column / Object / Time-Series]

DATA CHARACTERISTICS:
- Shape: [structured / semi-structured / graph / time-series / objects]
- Volume: [estimated size]
- Schema stability: [fixed / evolving / highly variable]

ACCESS PATTERNS:
- Read/Write ratio: [read-heavy / write-heavy / balanced]
- Query complexity: [simple lookups / aggregations / joins / traversals]
- Latency requirement: [real-time / near-real-time / batch OK]

CONSISTENCY REQUIREMENT:
- Level: [strong ACID / eventual / tunable]
- CAP priority: [CA / CP / AP]

SCALABILITY:
- Growth: [vertical sufficient / horizontal needed]
- Distribution: [single node / cluster / multi-region]

ALTERNATIVES CONSIDERED:
- [Alternative 1]: Rejected because [reason]
- [Alternative 2]: Rejected because [reason]

TRADE-OFFS ACCEPTED:
- [What we sacrifice and why it's acceptable for this use case]
```

---

## STEP 5: ANTI-PATTERNS — COMMON WRONG CHOICES

### STOP the user if you detect these patterns:

**"MongoDB for everything"**
→ MongoDB is not a relational database replacement. If the user has many JOINs, referential integrity needs, or financial transactions, push back.

**"PostgreSQL for everything because it's safe"**
→ PostgreSQL is excellent but not optimal for: graph traversals (use Neo4j), massive write throughput (use Cassandra), sub-millisecond caching (use Redis), or highly variable schemas at scale (use MongoDB).

**"Redis as primary database"**
→ Redis is a cache and data structure store. Using it as the sole persistent database risks data loss unless persistence is carefully configured. Always pair with a persistent store.

**"Neo4j for tabular data"**
→ If the primary operations are table scans, aggregations, or simple CRUD without relationship traversal, a graph database adds complexity without benefit.

**"Cassandra for ad-hoc queries"**
→ Cassandra requires designing one table per query pattern. If the user doesn't know their access patterns in advance, Cassandra will be painful.

**"Single database for a complex system"**
→ If the system has 3+ fundamentally different data access patterns (e.g., user auth + recommendations + analytics), suggest polyglot persistence.

**"Choosing by popularity instead of fit"**
→ "Everyone uses MongoDB" is not a justification. Every choice must be defended with data shape, access patterns, and consistency needs.

---

## RESPONSE BEHAVIOR

When this skill triggers:

1. **NEVER jump straight to code.** Always start with the decision framework.
2. **Ask clarifying questions** if the use case is ambiguous (use STEP 1).
3. **Recommend the database type first**, then a specific technology.
4. **Always produce a justification** (STEP 4 template), even for simple cases.
5. **Suggest polyglot architecture** when appropriate (STEP 3).
6. **Call out anti-patterns** proactively (STEP 5).
7. **After the decision is justified**, THEN help with schemas, queries, and code.
8. **If the user already chose a database**, validate it against their requirements before proceeding. If it's a poor fit, say so clearly with an alternative.

---

## QUICK REFERENCE: ONE-LINE DECISION GUIDE

- **"I need ACID transactions"** → PostgreSQL/MySQL
- **"My schema changes a lot"** → MongoDB
- **"I need it in < 1ms"** → Redis
- **"Relationships ARE my data"** → Neo4j
- **"Millions of writes/sec, global"** → Cassandra
- **"Complex objects with inheritance"** → IRIS
- **"Time-indexed metrics & monitoring"** → InfluxDB
- **"I have 3+ different data needs"** → Polyglot architecture
