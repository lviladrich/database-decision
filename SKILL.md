---
name: database-decision
description: >
  Guide developers through choosing the right database BEFORE writing any code.
  Covers SQL (PostgreSQL, MySQL), NoSQL document (MongoDB), graph (Neo4j), key-value (Redis),
  wide-column (Cassandra), object-oriented (IRIS), time-series (InfluxDB), and polyglot persistence.
  ALWAYS use this skill when: user asks "which database should I use", starts building without
  mentioning a DB choice, compares databases (MongoDB vs PostgreSQL), designs system architecture,
  mentions CAP theorem / ACID vs BASE / sharding / replication, says "I need a database for...",
  builds e-commerce / IoT / social / analytics / real-time systems, mentions "store data" / "persist
  data" / "data layer" / "polyglot persistence". Even if the user already picked a database, STOP
  and validate that choice before helping implement it. The wrong database is the most expensive
  technical debt.
---

# Database Decision Framework

## CRITICAL: DECIDE BEFORE YOU CODE

**STOP.** Before writing any database code, schema, or query — justify the choice first. Perfect code on the wrong foundation is technical debt from day one.

## STEP 1: GATHER REQUIREMENTS

Ask the user if not provided:

- **Data shape:** structured / semi-structured / graph / time-series / objects?
- **Schema stability:** fixed or frequently changing?
- **Read/Write ratio:** read-heavy, write-heavy, balanced?
- **Volume & scale:** MBs→PBs? Users: tens→millions? Single region or global?
- **Consistency needs:** strict ACID required? Eventual consistency acceptable?
- **Business:** team expertise, budget, existing infra, time pressure?

## STEP 2: SELECTION MATRIX

### Relational (PostgreSQL, MySQL) → STRUCTURED + STRONG CONSISTENCY
**Choose when:** stable schema, ACID mandatory, complex JOINs, referential integrity critical.
**CAP:** CA. **Signals:** "users table", "orders", "bank transactions", "billing", "ERP".
**AVOID:** schema changes often, hierarchical data, need 100s of nodes, millions writes/sec.

### Document (MongoDB) → FLEXIBLE SCHEMA + HIERARCHICAL DATA
**Choose when:** variable attributes per record, nested/JSON data, schema evolves fast, need sharding.
**CAP:** CP. **Sharding:** by range, hash, or geographic.
**Signals:** "product catalog", "user profiles", "CMS", "different fields per record".
**AVOID:** highly relational with many JOINs, simple key-value pairs.

### Key-Value (Redis) → SPEED + SIMPLE ACCESS
**Choose when:** sub-millisecond latency, get/set by key, caching, sessions, counters, leaderboards, queues.
**CAP:** CP (cluster). **Structures:** Strings, Lists, Sets, Hashes, Sorted Sets, Streams.
**Signals:** "cache", "sessions", "real-time", "leaderboard", "rate limiter", "shopping cart".
**AVOID:** data exceeds RAM, complex queries needed, need persistent-first storage.

### Graph (Neo4j) → RELATIONSHIP-CENTRIC DATA
**Choose when:** relationships ARE the value, path-finding, recommendations, fraud detection, social networks.
**CAP:** CA. **Clustering:** Core servers (Raft, M=2F+1) + Read Replicas. Causal consistency.
**Signals:** "relationships", "recommendations", "fraud", "knowledge graph", "who knows whom".
**AVOID:** massive distributed writes, tabular data without relationships, bulk updates on millions of nodes.

### Wide-Column (Cassandra) → MASSIVE WRITES + GLOBAL DISTRIBUTION
**Choose when:** write-heavy (IoT, logs, metrics), multi-datacenter, known query patterns, linear scaling, zero downtime.
**CAP:** AP. **Consistency tuning:** R+W>N = strong; W=1,R=N = fast writes; Quorum: W>N/2.
**Signals:** "IoT", "sensor data", "event log", "metrics", "multi-region", "always available".
**AVOID:** ad-hoc queries, JOINs, unpredictable read patterns, frequently changing data model.

### Object DB (InterSystems IRIS) → COMPLEX OBJECT PERSISTENCE
**Choose when:** deep OOP hierarchies, no ORM needed, complex domains (healthcare, finance, manufacturing).
**Signals:** "complex objects", "inheritance", "medical records", "financial instruments", "multi-model".
**AVOID:** simple structures, need max horizontal scaling, open-source requirement.

### Time-Series (InfluxDB) → TIME-INDEXED DATA + ANALYTICS
**Choose when:** data indexed by time, monitoring/metrics/IoT, downsampling, retention policies, scientific data.
**Indexing:** R-trees, KD-trees, LSM-trees. **Compression:** 90%+ reduction.
**Signals:** "metrics", "monitoring", "Grafana", "dashboards", "sensor data over time".
**AVOID:** data not time-indexed, need relationships, general-purpose CRUD.

## STEP 3: POLYGLOT PERSISTENCE

Most real systems need **multiple databases**. For each component independently ask: what's the data shape, access pattern, consistency need, and performance requirement?

### Classic E-Commerce (Polyglot)

| Component | DB | Why |
|---|---|---|
| Users & Auth | PostgreSQL | ACID, strong consistency, stable schema |
| Product Catalog | MongoDB | Variable attributes, flexible schema |
| Shopping Cart | Redis | Sub-ms latency, temporary, high concurrency |
| Recommendations | Neo4j | Graph traversal: "users who bought X also bought Y" |
| Order History | Cassandra | High writes, time-partitioned, multi-region |
| System Metrics | InfluxDB | Time-indexed, downsampling, dashboards |

### ACID vs BASE by Component
- **ACID required:** user accounts, payments, orders, inventory
- **BASE acceptable:** recommendations, caches, sessions, analytics, feeds

## STEP 4: JUSTIFICATION TEMPLATE

ALWAYS produce this for every database choice:

```
COMPONENT: [Name]
DATABASE: [Technology] ([Type])
DATA: shape, volume, schema stability
ACCESS: R/W ratio, query type, latency need
CONSISTENCY: ACID/eventual/tunable — CAP priority
SCALE: vertical/horizontal, distribution
REJECTED ALTERNATIVES: [why each was worse]
TRADE-OFFS ACCEPTED: [what you sacrifice and why]
```

## STEP 5: ANTI-PATTERNS — STOP THE USER

- **"MongoDB for everything"** → Not a relational replacement. Push back if JOINs/ACID needed.
- **"PostgreSQL for everything"** → Not optimal for graph traversals, massive writes, sub-ms caching, or variable schemas at scale.
- **"Redis as primary DB"** → It's a cache. Always pair with persistent store.
- **"Neo4j for tabular data"** → No benefit without relationship traversal.
- **"Cassandra for ad-hoc queries"** → Requires one table per query pattern. Unknown patterns = pain.
- **"Single DB for complex system"** → 3+ different access patterns = suggest polyglot.
- **"Choosing by popularity"** → "Everyone uses X" is not a justification. Defend with data shape + access patterns.

## RESPONSE BEHAVIOR

1. **NEVER** jump to code. Start with decision framework.
2. **Ask** clarifying questions if ambiguous (STEP 1).
3. **Recommend type first**, then specific technology.
4. **Always produce justification** (STEP 4), even for simple cases.
5. **Suggest polyglot** when appropriate (STEP 3).
6. **Call out anti-patterns** proactively (STEP 5).
7. **Only after justification**, help with schemas/queries/code.
8. **If user already chose**, validate against requirements first.

## QUICK REFERENCE

- **"I need ACID"** → PostgreSQL/MySQL
- **"Schema changes a lot"** → MongoDB
- **"Need it in <1ms"** → Redis
- **"Relationships ARE my data"** → Neo4j
- **"Millions writes/sec, global"** → Cassandra
- **"Complex objects with inheritance"** → IRIS
- **"Time-indexed metrics"** → InfluxDB
- **"3+ different data needs"** → Polyglot architecture
