#  database-decision

**Stop coding. Start deciding.**

An AI agent skill that forces you to justify your database choice *before* writing a single line of code. Because the wrong database is the most expensive technical debt — it's easier to rewrite code than to migrate data.

## What it does

When you tell your AI agent "I need a database for my app", instead of jumping straight to `CREATE TABLE` or `db.collection.insertOne()`, this skill kicks in and walks you through:

1. **Requirements extraction** — data shape, scale, consistency needs, business constraints
2. **Technology selection** — SQL, MongoDB, Neo4j, Redis, Cassandra, IRIS, InfluxDB
3. **CAP theorem analysis** — what you're trading off and why
4. **Polyglot architecture** — when one database isn't enough
5. **Written justification** — a structured rationale you can put in your docs
6. **Anti-pattern detection** — catches "MongoDB for everything" and other common mistakes

Only *after* the decision is justified does the agent help you with schemas, queries, and code.

## Install

```bash
npx skills add YOUR_USERNAME/database-decision
```

Works with any agent that supports [skills.sh](https://skills.sh): Claude Code, Cursor, Windsurf, Cline, etc.

## Triggers

The skill activates automatically when you:

- Ask "which database should I use?"
- Start building a system without mentioning a database choice
- Compare databases ("MongoDB vs PostgreSQL")
- Design architectures with data storage needs
- Mention CAP theorem, ACID vs BASE, sharding, or replication
- Describe any use case involving data persistence

## Coverage

| Type | Technologies | Best For |
|---|---|---|
| Relational | PostgreSQL, MySQL | ACID transactions, structured data, JOINs |
| Document | MongoDB | Flexible schemas, hierarchical data, catalogs |
| Key-Value | Redis | Sub-ms caching, sessions, leaderboards, queues |
| Graph | Neo4j | Relationships, recommendations, fraud detection |
| Wide-Column | Cassandra | Massive writes, global distribution, IoT/metrics |
| Object | InterSystems IRIS | Complex OOP hierarchies, healthcare, finance |
| Time-Series | InfluxDB | Monitoring, sensors, scientific data, dashboards |

## Example

**You say:**
> I'm building a food delivery app. Help me set up the database.

**Without this skill:** Agent starts writing a PostgreSQL schema immediately.

**With this skill:** Agent asks about your components (users, orders, restaurants, tracking, recommendations), analyzes each one separately, recommends a polyglot architecture (PostgreSQL for orders + MongoDB for restaurant menus + Redis for real-time tracking + Neo4j for recommendations), produces a written justification per component, and *then* helps you code.

## Academic Foundation

Built from the curriculum of **Ingeniería de Datos II** (Data Engineering II), a university course covering:

- CAP theorem (Brewer's conjecture, Gilbert & Lynch proof)
- ACID vs BASE consistency models
- N/R/W parameters and quorum-based consistency
- Distributed architectures (shared nothing, shared disk, clustering)
- Polyglot persistence with real-world case studies
- Hands-on work with MongoDB, Neo4j, Cassandra, Redis, IRIS, and InfluxDB

## Philosophy

> "AI can code. Humans must decide consciously."

AI agents are incredibly good at generating database schemas and queries. But they'll happily build a perfect MongoDB schema for a problem that needs PostgreSQL, or set up a single PostgreSQL instance for a problem that needs five different databases. This skill exists to insert a **decision checkpoint** between "I need to store data" and "here's your code."

## License

MIT

## Contributing

Issues and PRs welcome. If your university course covers database decision-making and you want to contribute frameworks or case studies, open an issue.
