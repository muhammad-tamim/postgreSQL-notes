<h1 align="center">PostgreSQL Notes</h1>

- [Setup:](#setup)
- [Introduction:](#introduction)
  - [What is PostgreSQL:](#what-is-postgresql)
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
        - [CROSS JOIN:](#cross-join)
    - [Common Select Functions:](#common-select-functions)
    - [Subqueries](#subqueries)
    - [CASE:](#case)
  - [UPDATE (PATCH/PUT):](#update-patchput)
    - [PATCH (partial update - recommended):](#patch-partial-update---recommended)
    - [PUT (Full Replace):](#put-full-replace)
  - [DELETE:](#delete)
- [Indexing:](#indexing)

# Setup: 
- Step 1: Download postgres and install:
![alt text](./assets/images/setup/postgres-download.png)

- Step 2: We can use postgres builtin GUI pgAdmin4 or Beekeeper Studio: 

![alt text](./assets/images/setup/pgadmin4.png)
![alt text](./assets/images/setup/beekeeper-stdio.png)

Note: Beekeeper Studio if more preferable. 

# Introduction:
PostgreSQL (often called Postgres) is a relational database management system (RDBMS). 

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

##### CROSS JOIN: 
Returns ALL possible combinations (Cartesian product) of rows from both tables.
- No ON condition needed
- Every row from users pairs with every row from orders

```sql
SELECT users.name, orders.amount
FROM users
CROSS JOIN orders;
```

| name  | amount |
| ----- | ------ |
| Tamim | 100    |
| Tamim | 200    |
| Tamim | 150    |
| Tamim | 300    |
| John  | 100    |
| John  | 200    |
| John  | 150    |
| John  | 300    |
| Sara  | 100    |
| Sara  | 200    |
| Sara  | 150    |
| Sara  | 300    |

here, 

- users = 3 rows
- orders = 4 rows
- Result = 3 × 4 = 12 rows

This is not a “relationship-based” join like others—this is a combinatorial expansion.

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

# Indexing: 
An index is a special data structure that helps PostgreSQL find rows faster.
-  Without index: full table scan (slow)
- With index: direct lookup (fast)

Syntax: 

```sql
CREATE INDEX index_name
ON table_name(column_name);
```


- Example:

```sql
CREATE INDEX idx_users_email ON users(email);
```

Now email search becomes fast:

```js
app.get("/users/email", async (req, res) => {
  try {
    const email = req.query.email;

    const result = await pool.query(
      `SELECT * FROM users WHERE email = $1`,
      [email]
    );

    res.send(result.rows);
  } catch (err) {
    console.error(err);
    res.status(500).send("Server Error");
  }
});
```

