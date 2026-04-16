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
- [CRUD Operation:](#crud-operation)
  - [CREATE (POST, INSERT):](#create-post-insert)
    - [Insert One:](#insert-one)
    - [Insert Many:](#insert-many)
    - [UPSERT:](#upsert)
  - [READ (GET, SELECT):](#read-get-select)
    - [Get All Rows:](#get-all-rows)
    - [Get Single Row:](#get-single-row)
    - [Clause:](#clause)
      - [Filtering (WHERE):](#filtering-where)
        - [With Arithmetic Operators (+, -, \*, /, %):](#with-arithmetic-operators------)
        - [With Comparison Operators (=, !=, \<, \>, \<=, \>=):](#with-comparison-operators------)
        - [With Logical Operators (AND, OR, NOT):](#with-logical-operators-and-or-not)
        - [With Range Operators (BETWEEN, NOT BETWEEN, IN, NOT IN):](#with-range-operators-between-not-between-in-not-in)
        - [With Pattern Operators (LIKE, ILIKE, NOTLIKE, ,NOTILIKE):](#with-pattern-operators-like-ilike-notlike-notilike)
      - [Sorting:](#sorting)
        - [DISTINCT:](#distinct)
        - [ORDER BY:](#order-by)
        - [LIMIT \& OFFSET](#limit--offset)
      - [aggregations:](#aggregations)
      - [Joins:](#joins)
        - [INNER JOIN:](#inner-join)
        - [LEFT JOIN](#left-join)
        - [RIGHT JOIN](#right-join)
        - [FULL JOIN](#full-join)
    - [Common Select Functions:](#common-select-functions)
    - [Subqueries](#subqueries)
    - [CASE:](#case)
  - [UPDATE (PATCH/PUT):](#update-patchput)
    - [PATCH (partial update - recommended):](#patch-partial-update---recommended)
    - [PUT (Full Replace):](#put-full-replace)
  - [DELETE:](#delete)

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


# CRUD Operation:
## CREATE (POST, INSERT): 

### Insert One: 

- Without API: 
  
```sql
INSERT INTO users (name, email)
VALUES ('Tamim', 'tamim@email.com')
RETURNING *;
```
- With API: 

```js
app.post("/users", async (req, res) => {
  try {
    const { name, email } = req.body;

    const result = await pool.query(
      `INSERT INTO users (name, email)
       VALUES ($1, $2)
       RETURNING *`,
      [name, email]
    );

    res.send(result.rows[0]);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```

Note: If we want custom returning: 

```js
const result = await pool.query(
  "INSERT INTO users (name, email) VALUES ($1, $2) RETURNING id, name",
  [name, email]
);
```

### Insert Many:

- Without API: 

```sql
INSERT INTO users (name, email)
VALUES
  ('Tamim', 'tamim@email.com'),
  ('John', 'john@email.com'),
  ('Sara', 'sara@email.com');
RETURNING *;  
```

- With API: 

```js
app.post("/users/bulk", async (req, res) => {
  try {
    const users = req.body; // [{ name, email }, ...]

    const values = users
      .map((_, i) => `($${i * 2 + 1}, $${i * 2 + 2})`)
      .join(",");

    const flatValues = users.flatMap(u => [u.name, u.email]);

    const result = await pool.query(
      `INSERT INTO users (name, email)
       VALUES ${values}
       RETURNING *`,
      flatValues
    );

    res.send(result.rows);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```

### UPSERT: 
- Without API: 

```sql
-- update if exist
INSERT INTO users (email, name)
VALUES ('tamim@email.com', 'Tamim')
ON CONFLICT (email)
DO UPDATE SET name = EXCLUDED.name
RETURNING *;
```
```sql
-- Do nothing if exists
INSERT INTO users (email, name)
VALUES ('tamim@email.com', 'Tamim')
ON CONFLICT (email)
DO NOTHING
RETURNING *;
```

- With API: 

```js
//  update if exist
app.post("/users/upsert", async (req, res) => {
  try {
    const { name, email } = req.body;

    const result = await pool.query(
      `
      INSERT INTO users (name, email)
      VALUES ($1, $2)
      ON CONFLICT (email)
      DO UPDATE SET 
        name = EXCLUDED.name
      RETURNING *;
      `,
      [name, email]
    );

    res.send(result.rows[0]);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```

```js
//  Do nothing if exists
app.post("/users/upsert", async (req, res) => {
  try {
    const { name, email } = req.body;

    const result = await pool.query(
      `
      INSERT INTO users (name, email)
      VALUES ($1, $2)
      ON CONFLICT (email)
      DO NOTHING
      RETURNING *;
      `,
      [name, email]
    );

    res.send(result.rows[0] || null);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```

**Note:** If we don't add Unique constrains to the schema upsert not work. 

## READ (GET, SELECT): 

### Get All Rows:

- Without API: 

```sql
SELECT * FROM users;
```

- Without API: 

```js
app.get("/users", async (req, res) => {
  try {
    const result = await pool.query("SELECT * FROM users");
    res.send(result.rows);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```

**Note:** If we want custom select: 

```sql
SELECT id, name FROM users;
```

### Get Single Row: 

- Without API: 

```sql
SELECT * FROM users WHERE id = 1;
```

- With API: 

```js
app.get("/users/:id", async (req, res) => {
  try {
    const result = await pool.query(
      "SELECT * FROM users WHERE id = $1", [req.params.id]
    );

    res.send(result.rows[0] || null);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```


### Clause: 
clause controls how data is retrieved.

#### Filtering (WHERE):

##### With Arithmetic Operators (+, -, *, /, %):

```js
app.get("/products/expensive", async (req, res) => {
  try {
    const result = await pool.query(
      `SELECT * FROM products WHERE price * quantity > 100`
    );

    res.send(result.rows);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```

##### With Comparison Operators (=, !=, <, >, <=, >=):

Note: != and <> are same

```js
app.get("/products/expensive", async (req, res) => {
  try {
    const result = await pool.query(
      `SELECT * FROM products WHERE price * quantity >= 100`
    );

    res.send(result.rows);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```


##### With Logical Operators (AND, OR, NOT):

- Without API: 
  
```sql
SELECT * FROM users WHERE country = 'USA' AND is_active = true;
```

```sql
SELECT * FROM users WHERE country = 'BD' OR country = 'UK';
```

```sql
SELECT * FROM users WHERE NOT country = 'USA';
```

- With API: 

```js
app.get("/users/active-usa", async (req, res) => {
  try {
    const result = await pool.query(
      `SELECT * FROM users WHERE country = $1 AND is_active = $2`,
      ["USA", true]
    );

    res.send(result.rows);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```

##### With Range Operators (BETWEEN, NOT BETWEEN, IN, NOT IN):

- Without API: 

```sql
SELECT * FROM users WHERE age BETWEEN 25 AND 30;
```

```sql
SELECT * FROM users WHERE country IN ('BD', 'USA', 'UK');
```

```sql
SELECT * FROM users
WHERE age 
  NOT BETWEEN 25 AND 30
  AND country NOT IN ('USA', 'UK');
```

- With API: 

```js
app.get("/users/age-range", async (req, res) => {
  try {
    const min = parseInt(req.query.min) || 20;
    const max = parseInt(req.query.max) || 30;

    const result = await pool.query(
      `SELECT * FROM users WHERE age BETWEEN $1 AND $2`,
      [min, max]
    );

    res.send(result.rows);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```

```js
app.get("/users/countries", async (req, res) => {
  try {
    const countries = req.query.countries?.split(",") || ["BD"];

    const result = await pool.query(
      `SELECT * FROM users WHERE country = ANY($1)`,
      [countries]
    );

    res.send(result.rows);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```

```js
app.get("/users/exclude", async (req, res) => {
  try {
    const result = await pool.query(
      `SELECT * FROM users
       WHERE 
        age NOT BETWEEN $1 AND $2
        AND country != ALL($3)`,
      [25, 30, ["USA", "UK"]]
    );

    res.send(result.rows);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```

##### With Pattern Operators (LIKE, ILIKE, NOTLIKE, ,NOTILIKE):

- LIKE → case-sensitive pattern match
- ILIKE → case-insensitive match (PostgreSQL-specific)
- NOT LIKE → exclude pattern (case-sensitive)
- NOT ILIKE → exclude pattern (case-insensitive)


Note: Wildcards (core concept)
- (%) → any number of characters
- (_) → exactly one character


```sql
SELECT * FROM users WHERE email LIKE '%@gmail.com'; -- % before → anything before domain
SELECT * FROM users WHERE name ILIKE '%tamim%'; -- Tamim, TAMIM, taMiM all valid
```


```js
app.get("/users/gmail", async (req, res) => {
  try {
    const result = await pool.query(
      `SELECT * FROM users WHERE email LIKE $1`,
      ['%@gmail.com']
    );

    res.send(result.rows);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```

```js
app.get("/users/search", async (req, res) => {
  try {
    const keyword = req.query.q || "";

    const result = await pool.query(
      `SELECT * FROM users WHERE name ILIKE $1`,
      [`%${keyword}%`]
    );

    res.send(result.rows);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```

#### Sorting: 

##### DISTINCT: 
DISTINCT is used to remove duplicate rows from the result set.


- Without API: 

```sql
-- single column
SELECT country FROM users;
-- multiple column
SELECT DISTINCT name, country FROM users;
```

- With API: 

```js
app.get("/countries", async (req, res) => {
  try {
    const result = await pool.query(
      "SELECT DISTINCT country FROM users"
    );

    res.send(result.rows);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
``` 

##### ORDER BY:
- Without API: 

```sql
SELECT * FROM users ORDER BY age ASC; -- Ascending - A, B, C
SELECT * FROM users ORDER BY age DESC; -- Descending - C, B, A

-- multiple column
SELECT * FROM users ORDER BY country ASC, age DESC;
```

- With API: 

```js
app.get("/users", async (req, res) => {
  try {
    const result = await pool.query(
      "SELECT * FROM users ORDER BY age ASC"
    );

    res.send(result.rows);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```

##### LIMIT & OFFSET
- LIMIT → how many rows to return
- OFFSET → how many rows to skip first

```js
app.get("/users", async (req, res) => {
  try {
    const page = parseInt(req.query.page) || 1;
    const limit = parseInt(req.query.limit) || 2;

    const offset = (page - 1) * limit;

    const result = await pool.query(
      "SELECT * FROM users LIMIT $1 OFFSET $2",
      [limit, offset]
    );

    res.send({
      page,
      limit,
      data: result.rows
    });
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```


#### aggregations:

- Aggregation Clause: 
  - GROUP BY: group rows that have the same value in a column and perform aggregations on them.
  - HAVING: filters groups AFTER grouping by using GROUP BY

- Common Aggregation Functions

| Function | Meaning        |
| -------- | -------------- |
| SUM()    | total          |
| COUNT()  | number of rows |
| AVG()    | average        |
| MAX()    | highest value  |
| MIN()    | lowest value   |


```sql
SELECT user_id, SUM(amount) AS total_spent
FROM orders
GROUP BY user_id
HAVING SUM(amount) > 200;
```

Original Table:

| id  | user_id | amount | status  |
| --- | ------- | ------ | ------- |
| 1   | 1       | 100    | paid    |
| 2   | 1       | 200    | paid    |
| 3   | 2       | 150    | pending |
| 4   | 2       | 300    | paid    |
| 5   | 3       | 50     | paid    |


Result: 
| user_id | total_spent |
| ------- | ----------- |
| 1       | 300         |
| 2       | 450         |


Without HAVING: 

```sql
SELECT user_id, SUM(amount) AS total_spent
FROM orders
GROUP BY user_id;
```

| user_id | total_spent |
| ------- | ----------- |
| 1       | 300         |
| 2       | 450         |
| 3       | 50          |


#### Joins:
A JOIN is used to combine rows from two or more tables based on a related column.

Example Tables: 

users: 
| id  | name  |
| --- | ----- |
| 1   | Tamim |
| 2   | John  |
| 3   | Sara  |


orders: 
| id  | user_id | amount |
| --- | ------- | ------ |
| 1   | 1       | 100    |
| 2   | 1       | 200    |
| 3   | 2       | 150    |
| 4   | 5       | 300    |


##### INNER JOIN: 
Returns ONLY matching rows in both tables.

```sql
SELECT users.name, orders.amount
FROM users
INNER JOIN orders
ON users.id = orders.user_id;
```


| name  | amount |
| ----- | ------ |
| Tamim | 100    |
| Tamim | 200    |
| John  | 150    |

here, Sara is missing because of no orders and user_id = 5 is ignored because of no matching user


##### LEFT JOIN
Returns ALL rows from LEFT table (users) + matches from right table, If no match → NULL

```sql
SELECT users.name, orders.amount
FROM users
LEFT JOIN orders
ON users.id = orders.user_id;
```

| name  | amount |
| ----- | ------ |
| Tamim | 100    |
| Tamim | 200    |
| John  | 150    |
| Sara  | NULL   |


##### RIGHT JOIN
Returns ALL rows from RIGHT table (orders) + matches from left table. If no match → NULL

```sql
SELECT users.name, orders.amount
FROM users
RIGHT JOIN orders
ON users.id = orders.user_id;
```

| name  | amount |
| ----- | ------ |
| Tamim | 100    |
| Tamim | 200    |
| John  | 150    |
| NULL  | 300    |


##### FULL JOIN
Returns:
- ALL rows from LEFT table
- ALL rows from RIGHT table
- Matches where possible
- Otherwise NULL

```sql
SELECT users.name, orders.amount
FROM users
FULL JOIN orders
ON users.id = orders.user_id;
```

| name  | amount |
| ----- | ------ |
| Tamim | 100    |
| Tamim | 200    |
| John  | 150    |
| Sara  | NULL   |
| NULL  | 300    |


### Common Select Functions:

```sql
// Aggregate Functions:
COUNT(), SUM(), AVG(), MIN(), MAX()

// Mathematical Functions:
ABS(), ROUND(), CEIL(), FLOOR(), POWER(), SQRT(), MOD(), RANDOM(), SIGN()

// String Functions:
CONCAT(), LENGTH(), LOWER(), UPPER(), TRIM(), SUBSTRING(), REPLACE(), POSITION(), LPAD(), RPAD()

// Date & Time Functions:
NOW(), CURRENT_DATE, CURRENT_TIME, AGE(), DATE_PART(), DATE_TRUNC(), EXTRACT()

// Conditional Functions:
COALESCE(), NULLIF(), GREATEST(), LEAST()

// Type Conversion Functions:
CAST(), TO_CHAR(), TO_DATE(), TO_NUMBER()

// Array Functions:
ARRAY_APPEND(), ARRAY_PREPEND(), ARRAY_REMOVE(), ARRAY_REPLACE(), ARRAY_LENGTH(), UNNEST()
```

### Subqueries
A subquery is a query written inside another query.

Users: 
| id  | name  | age |
| --- | ----- | --- |
| 1   | Tamim | 25  |
| 2   | John  | 30  |
| 3   | Sara  | 22  |

orders: 
| id  | user_id | amount |
| --- | ------- | ------ |
| 1   | 1       | 100    |
| 2   | 1       | 200    |
| 3   | 2       | 150    |


```sql
SELECT *
FROM users
WHERE id IN (
  SELECT user_id
  FROM orders
);
```

| id  | name  |
| --- | ----- |
| 1   | Tamim |
| 2   | John  |

### CASE: 
CASE is SQL’s way of doing if-else logic inside queries.

Syntax: 

```sql
CASE
  WHEN condition THEN result
  WHEN condition THEN result
  ELSE result
END
```


- Example 1: 

| id  | name  | age | country |
| --- | ----- | --- | ------- |
| 1   | Tamim | 25  | BD      |
| 2   | John  | 17  | USA     |
| 3   | Sara  | 30  | UK      |
| 4   | Alex  | 15  | USA     |


```sql
SELECT
  name,
  age,
  CASE
    WHEN age >= 18 THEN 'Adult'
    ELSE 'Minor'
  END AS age_group
FROM users;
```

Result: 
| name  | age | age_group |
| ----- | --- | --------- |
| Tamim | 25  | Adult     |
| John  | 17  | Minor     |
| Sara  | 30  | Adult     |
| Alex  | 15  | Minor     |


Example 2: 

| id  | amount | status  |
| --- | ------ | ------- |
| 1   | 100    | paid    |
| 2   | 200    | pending |
| 3   | 300    | failed  |


```sql
SELECT
  id,
  amount,
  status,
  CASE
    WHEN status = 'paid' THEN '✅ Completed'
    WHEN status = 'pending' THEN '⏳ Processing'
    WHEN status = 'failed' THEN '❌ Rejected'
    ELSE 'Unknown'
  END AS status_label
FROM orders;
```

Result: 

| id  | amount | status  | status_label |
| --- | ------ | ------- | ------------ |
| 1   | 100    | paid    | ✅ Completed  |
| 2   | 200    | pending | ⏳ Processing |
| 3   | 300    | failed  | ❌ Rejected   |

- Example 3: With where

```sql
SELECT *
FROM users
WHERE
  CASE
    WHEN age >= 18 THEN true
    ELSE false
  END = true;
```

- Example 4: with ORDER BY

```sql
SELECT *
FROM orders
ORDER BY
  CASE
    WHEN status = 'paid' THEN 1
    WHEN status = 'pending' THEN 2
    WHEN status = 'failed' THEN 3
    ELSE 4
  END;
```

- Example 5: With Aggregation

```sql
SELECT
  COUNT(*) AS total_users,
  SUM(CASE WHEN age >= 18 THEN 1 ELSE 0 END) AS adults,
  SUM(CASE WHEN age < 18 THEN 1 ELSE 0 END) AS minors
FROM users;
```

## UPDATE (PATCH/PUT):
### PATCH (partial update - recommended):

```js
app.patch("/notes/:id", async (req: Request, res: Response) => {
    try {
        const id = req.params.id;
        const { name, description } = req.body;

        const result = await pool.query(
            `UPDATE notes 
             SET 
                name = COALESCE($1, name),
                description = COALESCE($2, description)
             WHERE id = $3
             RETURNING *`,
            [name ?? null, description ?? null, id]
        );

        res.send({
            success: true,
            message: "Note updated",
            data: result.rows[0]
        });

    } catch (error: any) {
        res.status(500).send({
            success: false,
            message: error.message
        });
    }
});
```

### PUT (Full Replace):

```js
app.put("/notes/:id", async (req: Request, res: Response) => {
    try {
        const id = req.params.id;
        const { name, description } = req.body;

        const result = await pool.query(
            `INSERT INTO notes (id, name, description)
             VALUES ($1, $2, $3)
             ON CONFLICT (id)
             DO UPDATE SET
                name = EXCLUDED.name,
                description = EXCLUDED.description
             RETURNING *`,
            [id, name, description]
        );

        res.send({
            success: true,
            message: "Note replaced",
            data: result.rows[0]
        });

    } catch (error: any) {
        res.status(500).send({
            success: false,
            message: error.message
        });
    }
});
```
## DELETE: 

```js
app.delete("/notes/:id", async (req: Request, res: Response) => {
    try {
        const id = req.params.id;

        const result = await pool.query(
            "DELETE FROM notes WHERE id = $1 RETURNING *",
            [id]
        );

        if (result.rows.length === 0) {
            return res.status(404).send({
                success: false,
                message: "Note not found"
            });
        }

        res.send({
            success: true,
            message: "Note deleted",
            data: result.rows[0]
        });

    } catch (error: any) {
        res.status(500).send({
            success: false,
            message: error.message
        });
    }
});
```