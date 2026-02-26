# Database Decision

A skill that guides AI agents through choosing the right database technology **before** writing any code. Covers 7 database types across the full NoSQL and SQL spectrum.

This is a **meta-skill** — it runs before other database skills. While skills like `postgres-best-practices` or `nosql-expert` teach you how to *use* a database, this one teaches you how to *choose* which database to use. It's Step Zero.

> Other skills teach you to build. This skill teaches you to choose.

## Use when:

- "Which database should I use?"
- "I'm building an app, help me set up the database"
- "MongoDB or PostgreSQL for my project?"
- Designing system architecture with data storage needs
- Starting a project without mentioning a database choice
- "I need a database for..." followed by any use case
- Building e-commerce, IoT, social, analytics, or real-time systems

## Categories covered:

| Type | Technologies | Best For |
|---|---|---|
| Relational | PostgreSQL, MySQL | ACID transactions, structured data, JOINs |
| Document | MongoDB | Flexible schemas, hierarchical data, catalogs |
| Key-Value | Redis | Sub-ms caching, sessions, leaderboards, queues |
| Graph | Neo4j | Relationships, recommendations, fraud detection |
| Wide-Column | Cassandra | Massive writes, global distribution, IoT/metrics |
| Object | InterSystems IRIS | Complex OOP hierarchies, healthcare, finance |
| Time-Series | InfluxDB | Monitoring, sensors, scientific data, dashboards |

## How it works:

1. Extracts requirements (data shape, scale, consistency, business constraints)
2. Maps requirements to the right database type using CAP theorem and ACID vs BASE
3. Recommends polyglot architecture when one database isn't enough
4. Produces a written justification per component
5. Detects anti-patterns ("MongoDB for everything", "Redis as primary DB")
6. Only after justification → helps with schemas, queries, and code

## Example:

**You say:** "I'm building a food delivery app. Help me set up the database."

**Without this skill:** Agent writes a PostgreSQL schema immediately.

**With this skill:** Agent analyzes each component separately and recommends: PostgreSQL for orders + MongoDB for restaurant menus + Redis for real-time tracking + Neo4j for recommendations. Each choice justified. Then code.


## Installation

```
npx skills add lviladrich/database-decision
```

## Why this skill exists

In the skills ecosystem, every skill tells you **how to do something**: how to optimize PostgreSQL, how to model in MongoDB, how to configure Redis. They all assume you already know what you're going to use. None of them stop at the step before.

This skill doesn't teach you how to use a database. It teaches you to **think before choosing one**.

It's a skill about skills. Step zero. The moment where you haven't written anything yet and the decision you make will define everything that comes after. Because once you choose, there are hundreds of skills to help you build. But if you chose wrong, none of those skills can save you.

Tools for building are everywhere. What's missing is a tool for deciding.

## Academic Foundation

Built from the curriculum of **Ingeniería de Datos II** (Data Engineering II), covering CAP theorem, ACID vs BASE, N/R/W quorum consistency, distributed architectures, and polyglot persistence with real-world case studies.

## Skill Structure

```
database-decision/
├── SKILL.md     - Decision framework for the agent (139 lines)
├── README.md    - This file
└── banner.svg   - Visual diagram
```

## License

MIT
