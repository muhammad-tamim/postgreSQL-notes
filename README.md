<h1 align="center">PostgreSQL Notes</h1>

- [Setup:](#setup)
- [Introduction:](#introduction)
	- [What is PostgreSQL:](#what-is-postgresql)
	- [Features of PostgreSQL:](#features-of-postgresql)
	- [What is ACID and BASE compliance:](#what-is-acid-and-base-compliance)
		- [ACID Compliance:](#acid-compliance)
		- [Base Compliance:](#base-compliance)
		- [Difference Between ACID and Base Compliance:](#difference-between-acid-and-base-compliance)
	- [PostgreSQL vs MySQL Vs MongoDB:](#postgresql-vs-mysql-vs-mongodb)
	- [Schema vs Query:](#schema-vs-query)
- [Database and Table:](#database-and-table)
	- [Database Operations in PostgreSQL:](#database-operations-in-postgresql)
	- [Table Operations in PostgreSQL](#table-operations-in-postgresql)
- [Data Types in PostgresSQL:](#data-types-in-postgressql)

# Setup: 
- Step 1: Download postgres and install:
![alt text](./assets/images/setup/postgres-download.png)

- Step 2: We can use postgres builtin GUI pgAdmin4 or Beekeeper Studio: 

![alt text](./assets/images/setup/pgadmin4.png)
![alt text](./assets/images/setup/beekeeper-stdio.png)

Note: Beekeeper Studio if more preferable. 

# Introduction:
## What is PostgreSQL:
PostgreSQL (often called Postgres) is a relational database management system (RDBMS) that stores data in tables (rows and columns) format by using SQL (structure query language). 

## Features of PostgreSQL: 
- Relational Database with SQL + Advance SQL
- ACID Compliance
- Support complex relationships and queries
- Support both relational + JSON data
- Support Custom functions and data types
- Support Indexing (e.g. B-tree, Hash, GIN, GiST)


## What is ACID and BASE compliance: 
### ACID Compliance:  
ACID is a set of principles designed to ensure reliable and consistent database transactions. ACID stands for:
- Atomicity → A transaction is all or nothing (If one part fails, everything is rolled back)
- Consistency → Data always follows defined rules and constraints (e.g. schema)
- Isolation → Multiple transactions do not interfere with each other
- Durability → committed data is permanently saved (even after crashes)


### Base Compliance:
BASE is a set of principles designed for high availability and scalability in distributed systems. BASE stands for:
- Basically Available → System remains available even during failures
- Soft State → Data can change over time
- Eventually Consistent → Data will become consistent after some time

**Summary:**
- ACID: Focuses on strict consistency and data correctness
- BASE: Focuses on availability and scalability with eventual consistency

### Difference Between ACID and Base Compliance: 
| Feature        | ACID                              | BASE                                    |
| -------------- | --------------------------------- | --------------------------------------- |
| Consistency    | Strong (immediate)                | Eventual (delayed)                      |
| Availability   | Lower (can block for consistency) | High (always available)                 |
| Data Integrity | Strict                            | Relaxed                                 |
| Transactions   | Fully supported                   | Limited / weaker                        |
| Use Case       | Banking, payments, orders         | Social media, analytics, real-time apps |
| Database Type  | Relational (PostgreSQL, MySQL)    | NoSQL (MongoDB, Cassandra)              |


## PostgreSQL vs MySQL Vs MongoDB: 
| Feature        | PostgreSQL                     | MySQL                   | MongoDB                |
| -------------- | ------------------------------ | ----------------------- | ---------------------- |
| Type           | Relational (RDBMS)             | Relational (RDBMS)      | Document-based         |
| Schema         | Strict (but flexible via JSON) | Strict                  | Schema-less            |
| Query Language | SQL (advanced)                 | SQL (simpler)           | N/A                    |
| Transactions   | Full ACID support              | ACID (less advanced)    | Limited (improving)    |
| Relationships  | Strong (JOINs, FK)             | Supported               | Weak / manual          |
| JSON Support   | Yes (JSONB, powerful)          | Limited                 | Native                 |
| Performance    | Best for complex queries       | Fast for simple queries | Best for flexible data |

## Schema vs Query: 
- Schema: Define structure of our data
- Query: Any SQL instruction sent to the database

# Database and Table:
## Database Operations in PostgreSQL: 

```sql
-- Create Database
CREATE DATABASE database_name;

-- Rename Database
ALTER DATABASE old_name RENAME TO new_name;

-- Drop Database
DROP DATABASE database_name;

-- Drop Database if exists
DROP DATABASE IF EXISTS database_name;

-- Change database owner
ALTER DATABASE database_name OWNER TO new_owner;

-- Set database configuration (example)
ALTER DATABASE database_name SET timezone TO 'UTC';

-- Reset database configuration
ALTER DATABASE database_name RESET timezone;
```

## Table Operations in PostgreSQL

```sql
-- Create Table
CREATE TABLE users (...);

-- Create Table if not exists
CREATE TABLE IF NOT EXISTS users (...);

-- Rename Table
ALTER TABLE users RENAME TO customers;

-- Drop Table
DROP TABLE users;

-- Drop Table if exists
DROP TABLE IF EXISTS users;

-- Truncate Table: remove all data in the table
TRUNCATE TABLE users;

-- Add Column
ALTER TABLE users ADD COLUMN column_name TYPE;

-- Drop Column
ALTER TABLE users DROP COLUMN column_name;

-- Rename Column
ALTER TABLE users RENAME COLUMN old_name TO new_name;

-- Change Column Type
ALTER TABLE users ALTER COLUMN column_name TYPE NEW_TYPE;

-- Set Default Value
ALTER TABLE users ALTER COLUMN column_name SET DEFAULT value;

-- Drop Default Value
ALTER TABLE users ALTER COLUMN column_name DROP DEFAULT;

-- Set NOT NULL
ALTER TABLE users ALTER COLUMN column_name SET NOT NULL;

-- Drop NOT NULL
ALTER TABLE users ALTER COLUMN column_name DROP NOT NULL;

-- Add Constraint
ALTER TABLE users ADD CONSTRAINT constraint_name UNIQUE (column_name);

-- Drop Constraint
ALTER TABLE users DROP CONSTRAINT constraint_name;

-- Add Primary Key
ALTER TABLE users ADD PRIMARY KEY (column_name);

-- Drop Primary Key
ALTER TABLE users DROP CONSTRAINT table_name_pkey;

-- Add Foreign Key
ALTER TABLE table_name 
ADD CONSTRAINT fk_name 
FOREIGN KEY (column_name) REFERENCES ref_table(column_name);

-- Drop Foreign Key
ALTER TABLE table_name DROP CONSTRAINT fk_name;

-- Create Index
CREATE INDEX index_name ON table_name(column_name);

-- Drop Index
DROP INDEX index_name;

-- Create Temporary Table
CREATE TEMP TABLE temp_table_name (...);

-- Copy Table Structure
CREATE TABLE new_table LIKE existing_table;

-- Copy Table with Data
CREATE TABLE new_table AS SELECT * FROM existing_table;
```


# Data Types in PostgresSQL: 

| Type Category | Data Type          | Description                                                         | Example                                  |
| ------------- | ------------------ | ------------------------------------------------------------------- | ---------------------------------------- |
| Text          | `TEXT`             | Unlimited length string (preferred instead of char and varchar)     | `'Tamim'`                                |
| Text          | `VARCHAR(n)`       | Limited length string (0 to n)                                      | `'Tamim'`                                |
| Text          | `CHAR(n)`          | Fixed length string with white space (can be slower due to padding) | `'T    '`                                |
| Numeric       | `INT` / `INTEGER`  | Whole numbers                                                       | `25`                                     |
| Numeric       | `BIGINT`           | Large whole numbers                                                 | `9223372036854775807`                    |
| Numeric       | `SMALLINT`         | Small whole numbers                                                 | `120`                                    |
| Numeric       | `NUMERIC(p,s)`     | Precise decimal (money)                                             | `99.99`                                  |
| Numeric       | `DECIMAL`          | Same as NUMERIC                                                     | `10.50`                                  |
| Numeric       | `REAL`             | Floating point number                                               | `3.14`                                   |
| Numeric       | `DOUBLE PRECISION` | High precision float                                                | `3.1415926535`                           |
| Boolean       | `BOOLEAN`          | true/false                                                          | `TRUE`                                   |
| Date/Time     | `DATE`             | Date only                                                           | `'2026-04-12'`                           |
| Date/Time     | `TIME`             | Time only                                                           | `'14:30:00'`                             |
| Date/Time     | `TIMESTAMP`        | Date + time                                                         | `'2026-04-12 14:30:00'`                  |
| Date/Time     | `TIMESTAMPTZ`      | Timestamp with timezone                                             | `'2026-04-12 14:30:00+06'`               |
| Date/Time     | `INTERVAL`         | Time duration                                                       | `'2 days'`                               |
| UUID          | `UUID`             | Unique identifier                                                   | `'550e8400-e29b-41d4-a716-446655440000'` |
| JSON          | `JSON`             | JSON data (text format)                                             | `'{"name":"Tamim"}'`                     |
| JSON          | `JSONB`            | Binary JSON (faster, recommended)                                   | `'{"name":"Tamim"}'`                     |
| Array         | `TEXT[]`           | Array of strings                                                    | `{'a','b','c'}`                          |
| Array         | `INT[]`            | Array of integers                                                   | `{1,2,3}`                                |
| Binary        | `BYTEA`            | Binary data (images/files)                                          | `\xDEADBEEF`                             |
| Network       | `INET`             | IP address                                                          | `'192.168.1.1'`                          |
| Network       | `CIDR`             | Network address                                                     | `'192.168.0.0/24'`                       |
| Network       | `MACADDR`          | MAC address                                                         | `'08:00:2b:01:02:03'`                    |
| Money         | `MONEY`            | Currency type                                                       | `$100.00`                                |
| Full-text     | `TSVECTOR`         | Search optimized text                                               | `'fast search vector'`                   |
| Full-text     | `TSQUERY`          | Full-text query                                                     | `'postgres & sql'`                       |
| Geometric     | `POINT`            | X,Y coordinate                                                      | `(10,20)`                                |
| Geometric     | `LINE`             | Infinite line                                                       | `'{1,2,3}'`                              |
| Geometric     | `BOX`              | Rectangle                                                           | `'((0,0),(10,10))'`                      |

Note: 
- `name CHAR(10)`: Always uses full length n. SO if we insert `tamim` it will add white space for rest `'Tamim     '` to match always exactly 10 characters
- `name VARCHAR(10)`: Uses only required space of 0 to n. SO if we insert `tamim` it will don't give error unless 10 character.   


