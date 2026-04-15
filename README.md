<h1 align="center">PostgreSQL Notes</h1>

- [Setup:](#setup)
- [Introduction:](#introduction)
  - [What is PostgreSQL:](#what-is-postgresql)
  - [Features of PostgreSQL:](#features-of-postgresql)
  - [What is ACID and BASE compliance:](#what-is-acid-and-base-compliance)
    - [ACID Compliance:](#acid-compliance)
    - [Base Compliance:](#base-compliance)
    - [Difference Between ACID and Base Compliance:](#difference-between-acid-and-base-compliance)
- [Managing Database and Table:](#managing-database-and-table)
  - [Database:](#database)
    - [Create Database:](#create-database)
    - [Get Databases:](#get-databases)
    - [Rename Database:](#rename-database)
    - [Delete Database](#delete-database)
  - [Table:](#table)
    - [Create and view Table structure:](#create-and-view-table-structure)
    - [Delete and TRUNCATE Table:](#delete-and-truncate-table)
    - [ALTER TABLE:](#alter-table)
      - [Rename a table:](#rename-a-table)
      - [Rename Column:](#rename-column)
      - [Add Column:](#add-column)
      - [Remove Column:](#remove-column)
      - [Change Data Type:](#change-data-type)
      - [Add Constraint:](#add-constraint)
      - [Remove Constraint:](#remove-constraint)
- [Schema:](#schema)
  - [Common Data Types:](#common-data-types)
    - [Numeric types:](#numeric-types)
    - [String types:](#string-types)
    - [Date \& Time types:](#date--time-types)
    - [Others Types:](#others-types)
  - [Column Constraints:](#column-constraints)
    - [NOT NULL:](#not-null)
    - [UNIQUE:](#unique)
    - [PRIMARY KEY:](#primary-key)
    - [FOREIGN KEY:](#foreign-key)
      - [ON DELETE options:](#on-delete-options)
        - [ON DELETE CASCADE:](#on-delete-cascade)
        - [ON DELETE SET NULL:](#on-delete-set-null)
        - [ON DELETE RESTRICT:](#on-delete-restrict)
    - [CHECK:](#check)
    - [DEFAULT:](#default)
    - [SERIAL:](#serial)
    - [IDENTITY:](#identity)
- [CRUD Operations:](#crud-operations)
  - [Create (POST):](#create-post)
    - [INSERT INTO:](#insert-into)
      - [Insert single row:](#insert-single-row)
      - [Insert multiple row:](#insert-multiple-row)
  - [Read (GET):](#read-get)
    - [SELECT (Get All):](#select-get-all)
    - [SELECT WHERE (Get Single):](#select-where-get-single)
    - [SELECT Helpers:](#select-helpers)
    - [Filtering:](#filtering)
    - [aggregate():](#aggregate)
      - [Common Aggregation Stages:](#common-aggregation-stages)
    - [DISTINCT:](#distinct)
  - [UPDATE (PUT/PATCH):](#update-putpatch)
  - [Delete (DELETE):](#delete-delete)
  - [INSERT (Create)](#insert-create)
  - [SELECT (Read)](#select-read)
  - [UPDATE](#update)
  - [DELETE](#delete)

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


# Managing Database and Table:

## Database:
### Create Database: 

```sql
CREATE DATABASE my_app;
```

### Get Databases:

```sql
SELECT datname FROM pg_database;
```

### Rename Database: 

```sql
ALTER DATABASE my_app RENAME TO my_app_v2;
```

### Delete Database

```sql
DROP DATABASE my_app;
```

## Table:

### Create and view Table structure: 

- Create Table:

```sql
CREATE TABLE IF NOT EXISTS table_name (
  column_name data_type column_constraints
  column_name data_type column_constraints
  column_name data_type column_constraints
);
```

Creates a new table by copying structure and/or data from an existing table: 

```sql
-- Copy structure AND data
CREATE TABLE employees_backup AS SELECT * FROM employees;
```

```sql
-- Copy structure only (no data)
CREATE TABLE employees_backup AS SELECT * FROM employees WHERE 1 = 0;
-- The WHERE 1=0 trick copies columns but returns zero rows, giving you an empty copy of the table structure.
```

- View Table Structure: 

```sql
SELECT column_name, data_type
FROM information_schema.columns
WHERE table_name = 'users';
```

for views every table in the public schema (our default working schema).

```sql
SELECT table_name, table_type
FROM information_schema.tables
WHERE table_schema = 'public'
ORDER BY table_name
```

### Delete and TRUNCATE Table: 

- Delete: Permanently removes the table and all its data, 
  
```sql
DROP TABLE users;
-- or
DROP TABLE IF EXISTS users;
```

- Truncate: Deletes every row instantly but leaves the table structure intact. Much faster than DELETE for large tables.

```sql
TRUNCATE TABLE employees;
```
  
### ALTER TABLE:
Alter table means modify an existing table’s schema

####  Rename a table: 

```sql
ALTER TABLE employees
  RENAME TO staff;
```

#### Rename Column: 

```sql
ALTER TABLE users RENAME COLUMN name TO full_name;
```

#### Add Column: 

```sql
ALTER TABLE users ADD COLUMN age INT;
``` 

#### Remove Column: 

```sql
ALTER TABLE users DROP COLUMN age;
```

#### Change Data Type: 

```sql
ALTER TABLE users
ALTER COLUMN age TYPE BIGINT;
```

#### Add Constraint: 

```sql
ALTER TABLE users
ALTER COLUMN email SET NOT NULL;
```

```sql
ALTER TABLE users
ADD CONSTRAINT unique_email UNIQUE (email);
```

```sql
ALTER TABLE users
ALTER COLUMN is_active SET DEFAULT true;
```

#### Remove Constraint: 

```sql
ALTER TABLE users
DROP CONSTRAINT unique_email;
```

# Schema: 
A Schema defines the structure of our data. 

Syntax: 
```sql
CREATE TABLE IF NOT EXISTS users (
    column_name data_type/schema_type column_constraints,
    column_name data_type/schema_type column_constraints,
    ...
);
```
here, 
1. CREATE TABLE IF NOT EXISTS: Command to create a new table in the database if not exist
2. users: Table name
3. name, email are column names
4. data_type: Defines what kind of data the column stores
5. constraints: Rules applied to a column
 
## Common Data Types: 
### Numeric types:

| Type               | Description                                                 | Example                                  |
| ------------------ | ----------------------------------------------------------- | ---------------------------------------- |
| `SMALLINT`         | 2 bytes (small numbers)                                     | age, quantity etc only small numbers     |
| `INT`              | 4 bytes (most common)                                       | default choice                           |
| `BIGINT`           | 8 bytes (large numbers)                                     | large counters when overflow is possible |
| `NUMERIC/DECIMAL`  | variable (exact precision)                                  | money or exact values (always)           |
| `REAL`             | 4 bytes (less precise float, ~6 decimal digits precision)   | approximate scientific data only         |
| `DOUBLE PRECISION` | 8 Bytes (high precision float, 15 decimal digits precision) | approximate scientific data only         |
| `SERIAL`           | 4 bytes same as INT (Auto increment 1, 2, 3, 4)             | For PRIMARY KEY                          |
| ` BIGSERIAL`       | 8 bytes same as BIGINT (Auto increment 1, 2, 3, 4)          | For Large PRIMARY KEY                    |

```sql
CREATE TABLE IF NOT EXISTS numeric_types_demo (
    id SERIAL PRIMARY KEY,                      -- auto increment (INT)
    id BIGSERIAL PRIMARY KEY,                   -- auto increment (BIGINT)

    small_number SMALLINT,                      -- 10, -5, 300
    normal_number INT,                          -- 1000, 250000
    big_number BIGINT,                          -- 10000000000

    exact_money NUMERIC(10,2),                  -- 10.00, 10.99
    exact_precise NUMERIC(10,6),                -- 10.123456

    approx_real REAL,                           -- 10.123457 (rounded after 6 digits)
    approx_double DOUBLE PRECISION,             -- 10.123456789123457 (rounded after 15 digits)
);
```

### String types:

| Type         | Description                                   | Example                         |
| ------------ | --------------------------------------------- | ------------------------------- |
| `CHAR(n)`    | Fixed n length of string (padded with spaces) | fixed codes like `'A'`, `'USD'` |
| `VARCHAR(n)` | Variable n length string with limit           | names, titles (`'Tamim'`)       |
| `TEXT`       | Variable-length length string                 | descriptions, blog content      |


```sql
CREATE TABLE IF NOT EXISTS string_types_demo (
    id SERIAL PRIMARY KEY,                         -- auto increment

    fixed_char CHAR(5),                            -- 'A    ', 'BD   ' (padded)
    short_text VARCHAR(50),                        -- 'Hello World'
    long_text TEXT                                 -- large content
);
```

### Date & Time types:

| Type          | Description                             | Example                            |
| ------------- | --------------------------------------- | ---------------------------------- |
| `DATE`        | Stores date only (no time)              | `'2026-04-13'`                     |
| `TIME`        | Stores time only (no date)              | `'14:30:00'`                       |
| `TIMESTAMP`   | Date + time (no timezone)               | `'2026-04-13 14:30:00'`            |
| `TIMESTAMPTZ` | Date + time with timezone (recommended) | `'2026-04-13 14:30:00+06'`         |
| `INTERVAL`    | Duration / difference between times     | `'2 days'`, `'3 hours 30 minutes'` |

```sql
CREATE TABLE IF NOT EXISTS datetime_types_demo (
    id SERIAL PRIMARY KEY,                          -- auto increment

    only_date DATE,                                 -- '2026-04-13'
    only_time TIME,                                 -- '14:30:00'

    simple_timestamp TIMESTAMP,                     -- '2026-04-13 14:30:00'
    tz_timestamp TIMESTAMPTZ,                       -- '2026-04-13 14:30:00+06'

    duration INTERVAL                               -- '2 days', '3 hours'
);
```

### Others Types: 
| Type            | Description                                                      | Example                                                     |
| --------------- | ---------------------------------------------------------------- | ----------------------------------------------------------- |
| `BOOLEAN`       | Accepts true, false and null by: TRUE, FALSE, NULL, 1/0, 't'/'f' | is_active                                                   |
| `UUID`          | Universally unique identifier                                    | user_id, order_id: `'550e8400-e29b-41d4-a716-446655440000'` |
| `JSON`          | Stores JSON as text (text format, slower)                        | `'{"name": "Tamim"}'`                                       |
| `JSONB`         | Binary JSON (faster, indexable, recommended)                     | `'{"name": "Tamim"}'`                                       |
| `ARRAY: TYPE[]` | Stores multiple values in one column of same types               | `'{1,2,3}'`, `'{apple,banana}'`                             |
| `ENUM`          | Fixed set of predefined values                                   | `user_role: 'admin'`, `'seller'`                            |


```sql
-- ENUM must be created first
CREATE TYPE order_status AS ENUM ('pending', 'completed', 'cancelled');

CREATE TABLE IF NOT EXISTS other_types_demo (
    id SERIAL PRIMARY KEY,                          -- auto increment

    is_active BOOLEAN,                              -- true / false

    user_uuid UUID,                                 -- unique identifier

    json_data JSON,                                 -- raw JSON
    jsonb_data JSONB,                               -- optimized JSON

    int_array INT[],                                -- {1,2,3}
    text_array TEXT[],                              -- {'apple','banana'}

    status order_status                             -- ENUM
);
```


## Column Constraints: 

| Constraint    | Description                    | Example Use                   |
| ------------- | ------------------------------ | ----------------------------- |
| `NOT NULL`    | Prevent empty values           | username, email               |
| `UNIQUE`      | Prevent duplicate values       | email, username               |
| `PRIMARY KEY` | Unique identifier for each row | id column                     |
| `FOREIGN KEY` | Links to another table         | user_id → users.id            |
| `CHECK`       | Validates condition            | age > 18                      |
| `DEFAULT`     | Sets default value             | is_active = true              |
| `SERIAL `     | Auto-increment value           | id generation for primary key |
| `IDENTITY`    | Modern replacement for SERIAL  | id generation for primary key |

### NOT NULL: 
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT NOT NULL
);
``` 

### UNIQUE: 
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email TEXT UNIQUE
);
```

### PRIMARY KEY: 
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email TEXT
);
```

### FOREIGN KEY: 
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name TEXT
);

CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INT REFERENCES users(id)
);
``` 

Note 1: REFERENCES is just shorthand syntax for FOREIGN KEY

```sql
user_id INT REFERENCES users(id)
-- or
FOREIGN KEY (user_id) REFERENCES users(id)
```

#### ON DELETE options:
##### ON DELETE CASCADE:

If parent is deleted → automatically delete all related child rows

 ```sql
 user_id INT REFERENCES users(id)  ON DELETE CASCADE
 ```
 ##### ON DELETE SET NULL:
If parent is deleted → keep child rows, but set foreign key to NULL (column must allow NULL)

 ```sql
 user_id INT REFERENCES users(id) ON DELETE SET NULL
 ```
 ##### ON DELETE RESTRICT:
Prevent deleting the parent row if any child rows reference it

 ```sql
 user_id INT REFERENCES users(id) ON DELETE RESTRICT
 ```

### CHECK:

```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    price NUMERIC CHECK (price > 0),
    stock INT CHECK (stock >= 0)
);
```

### DEFAULT: 

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    is_active BOOLEAN DEFAULT TRUE, 
    created_at TIMESTAMP DEFAULT NOW() -- now() === CURRENT_TIMESTAMP 
);
```

### SERIAL: 

```sql
CREATE TABLE IF NOT EXISTS users (
    id SERIAL PRIMARY KEY,                          
);
```

### IDENTITY: 

```sql
CREATE TABLE users (
    id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY
);
```


# CRUD Operations:

## Create (POST): 
### INSERT INTO: 
####  Insert single row: 

With returning all: 

```sql
app.post("/notes", async (req: Request, res: Response) => {
  try {
    const { name, description } = req.body;

    const result = await pool.query(
      "INSERT INTO notes (name, description) VALUES ($1, $2) RETURNING *",
      [name, description]
    );

    res.send(result.rows[0]);
  } catch (err) {
    res.status(500).json({ error: "Failed to create note" });
  }
});
```

With custom returning: 

```sql
app.post("/notes", async (req: Request, res: Response) => {
  try {
    const { name, description } = req.body;

    const result = await pool.query(
      `INSERT INTO notes (name, description) VALUES ($1, $2) RETURNING id, name`,
      [name, description]
    );

    res.status(201).json(result.rows[0]);
  } catch (err) {
    res.status(500).json({ error: "Failed to create note" });
  }
});
```

#### Insert multiple row: 

```sql
app.post("/notes/bulk", async (req: Request, res: Response) => {
  try {
    const notes = req.body.notes; // [{name, description}, ...]

    const values: any[] = [];
    const placeholders = notes
      .map((note: any, i: number) => {
        const idx = i * 2;
        values.push(note.name, note.description);
        return `($${idx + 1}, $${idx + 2})`;
      })
      .join(", ");

    const query = `
      INSERT INTO notes (name, description)
      VALUES ${placeholders}
      RETURNING *
    `;

    const result = await pool.query(query, values);

    res.status(201).json(result.rows);
  } catch (err) {
    res.status(500).json({ error: "Bulk insert failed" });
  }
});
```

## Read (GET):
### SELECT (Get All): 
```sql
app.get("/users", async (_req: Request, res: Response) => {
  const result = await pool.query("SELECT * FROM users");
  res.json(result.rows);
});
```

### SELECT WHERE (Get Single): 

```sql
app.get("/users/:id", async (req: Request, res: Response) => {
  const result = await pool.query(
    "SELECT * FROM users WHERE id = $1",
    [req.params.id]
  );

  res.json(result.rows[0]);
});
```

### SELECT Helpers: 
### Filtering: 
### aggregate():
#### Common Aggregation Stages:

### DISTINCT: 

```sql
app.get("/users/emails", async (_req: Request, res: Response) => {
  const result = await pool.query(
    "SELECT DISTINCT email FROM users"
  );

  res.json(result.rows);
});
```


## UPDATE (PUT/PATCH):
## Delete (DELETE):



## INSERT (Create)
  - Basic insert
  - Multi-row insert
  - RETURNING clause
  - ON CONFLICT (UPSERT)

## SELECT (Read)
  - WHERE conditions
  - Filtering operators (IN, BETWEEN, LIKE, etc.)
  - ORDER BY, LIMIT, OFFSET
  - DISTINCT

## UPDATE
  - UPDATE with WHERE
  - RETURNING clause
  - CASE-based updates

## DELETE
  - DELETE with WHERE
  - RETURNING clause
  - Soft delete pattern