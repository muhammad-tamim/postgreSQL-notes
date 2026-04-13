
- [05. Querying \& Filtering (Intermediate)](#05-querying--filtering-intermediate)
- [06. Functions \& Operators](#06-functions--operators)
- [07. Indexes](#07-indexes)
- [08. Transactions \& Concurrency](#08-transactions--concurrency)
- [09. Views \& Materialized Views](#09-views--materialized-views)
- [10. Stored Procedures \& Triggers](#10-stored-procedures--triggers)
- [11. Advanced Features](#11-advanced-features)
- [12. Performance \& Optimization](#12-performance--optimization)
- [13. Security \& Roles](#13-security--roles)
- [14. Backup, Restore \& Replication](#14-backup-restore--replication)
- [15. PostgreSQL with Node.js](#15-postgresql-with-nodejs)
- [16. Extensions](#16-extensions)
- [17. Schema Design Patterns](#17-schema-design-patterns)
- [18. Monitoring \& Observability](#18-monitoring--observability)
- [19. Real-World Examples](#19-real-world-examples)


---

### 05. Querying & Filtering (Intermediate)
- JOINs (INNER, LEFT, RIGHT, FULL, CROSS)
- Subqueries (scalar, correlated, EXISTS, ANY/ALL)
- Aggregation (COUNT, SUM, AVG, GROUP BY, HAVING)
- Set operations (UNION, INTERSECT, EXCEPT)
- CTEs (WITH, RECURSIVE)
- Window functions (ROW_NUMBER, RANK, LAG/LEAD, PARTITION BY)
- Conditional expressions (CASE, COALESCE, NULLIF)

---

### 06. Functions & Operators
- String functions
- Numeric functions
- Date & time functions
- Type casting (CAST, ::)
- JSON & JSONB functions
- Array functions
- User-defined functions (PL/pgSQL basics)

---

### 07. Indexes
- What is an index?
- Index types (B-tree, GIN, GiST, BRIN, Hash)
- Creating indexes
- Composite and partial indexes
- EXPLAIN & query planning

---

### 08. Transactions & Concurrency
- Transactions (BEGIN, COMMIT, ROLLBACK)
- ACID properties
- Isolation levels
- Locking mechanisms
- MVCC (Multi-Version Concurrency Control)

---

### 09. Views & Materialized Views
- Views (CREATE VIEW, DROP VIEW)
- Materialized views
- Refresh strategies

---

### 10. Stored Procedures & Triggers
- Stored procedures (CALL, CREATE PROCEDURE)
- Functions vs procedures
- Triggers (BEFORE, AFTER, INSTEAD OF)
- Event triggers

---

### 11. Advanced Features
- Full-text search
- Partitioning
- Generated columns
- Row-Level Security (RLS)
- Foreign Data Wrappers (FDW)
- Logical replication

---

### 12. Performance & Optimization
- Query optimization (EXPLAIN ANALYZE)
- Connection pooling
- VACUUM & autovacuum
- Caching strategies
- Bulk operations (COPY)

---

### 13. Security & Roles
- Roles & users
- Privileges (GRANT / REVOKE)
- SSL & encryption
- Audit logging

---

### 14. Backup, Restore & Replication
- pg_dump / pg_restore
- PITR (Point-in-Time Recovery)
- Streaming replication
- High availability strategies

---

### 15. PostgreSQL with Node.js
- Setup (node-postgres)
- CRUD with pg
- Transactions in Express
- Input validation (Zod)
- ORM options (Prisma, Drizzle, Sequelize, Knex)

---

### 16. Extensions
- pgcrypto
- uuid-ossp
- pg_trgm
- PostGIS
- timescaledb
- pg_stat_statements
- pg_cron

---

### 17. Schema Design Patterns
- Normalization (1NF → BCNF)
- Soft delete pattern
- Audit fields
- Many-to-many relationships
- Multi-tenant design
- Event sourcing

---

### 18. Monitoring & Observability
- pg_stat_activity
- pg_stat_statements
- Logging configuration
- Performance metrics
- Monitoring tools

---

### 19. Real-World Examples
- Full CRUD API with Express + PostgreSQL
- Multi-table JOIN + pagination system
- Full-text search implementation
- Multi-tenant RLS example
- Recursive CTE (tree structure)
- Leaderboard using window functions
- Bulk import + UPSERT