# SQL Recognition Reference

A quick-reference guide organised by what you'll **see** on a test. SQL reads almost like English — the trick is knowing the exact vocabulary and the order things must appear.

---

## The Big Picture

SQL does four things to data in tables:

| Operation | Keyword | Plain English |
|-----------|---------|---------------|
| **Create** | `INSERT` | Add new rows |
| **Read** | `SELECT` | Retrieve rows |
| **Update** | `UPDATE` | Change existing rows |
| **Delete** | `DELETE` | Remove rows |

These are called **CRUD** operations. `SELECT` is by far the most tested.

---

## SELECT — Reading Data

### Basic structure (clauses must appear in this order):

```sql
SELECT columns          -- what you want
FROM table              -- where it lives
WHERE condition         -- which rows (filter before grouping)
GROUP BY column         -- collapse rows into groups
HAVING condition        -- filter after grouping
ORDER BY column         -- sort the results
LIMIT n;                -- how many rows to return
```

You won't use all of these every time. But when you do, **this order is mandatory.**

### Examples:

```sql
-- Get everything:
SELECT * FROM users;

-- Specific columns:
SELECT name, email FROM users;

-- With a filter:
SELECT name FROM users WHERE age > 30;

-- Sorted, limited:
SELECT name, age FROM users ORDER BY age DESC LIMIT 10;
```

---

## WHERE — Filtering Rows

```sql
WHERE age > 30                        -- Comparison: >, <, >=, <=, =, != (or <>)
WHERE name = 'Andy'                   -- Strings use single quotes
WHERE age BETWEEN 20 AND 30           -- Inclusive range
WHERE name IN ('Andy', 'Laura')       -- Match any value in list
WHERE name LIKE 'A%'                  -- Pattern match: % = any characters
WHERE name LIKE '_ndy'                -- _ = exactly one character
WHERE email IS NULL                   -- Check for missing values (NOT = NULL, IS NULL)
WHERE email IS NOT NULL               -- Has a value
WHERE age > 30 AND name = 'Andy'      -- Combine with AND, OR, NOT
WHERE NOT (age > 30 OR name = 'Andy') -- Parentheses control precedence
```

**Critical gotcha:** `NULL` is not a value — it's the *absence* of a value. You cannot use `= NULL` or `!= NULL`. You must use `IS NULL` / `IS NOT NULL`. This is a test favourite.

---

## JOINs — Combining Tables

This is the most important concept to recognise. Given two tables:

**users:** `id, name, department_id`
**departments:** `id, dept_name`

### INNER JOIN — only matching rows from both tables
```sql
SELECT users.name, departments.dept_name
FROM users
INNER JOIN departments ON users.department_id = departments.id;
```
If a user has no department, they're **excluded**. If a department has no users, it's **excluded**.

### LEFT JOIN — all rows from left table, matching from right
```sql
SELECT users.name, departments.dept_name
FROM users
LEFT JOIN departments ON users.department_id = departments.id;
```
Every user appears. If they have no department, `dept_name` is `NULL`.

### RIGHT JOIN — all rows from right table, matching from left
```sql
SELECT users.name, departments.dept_name
FROM users
RIGHT JOIN departments ON users.department_id = departments.id;
```
Every department appears. If a department has no users, `name` is `NULL`.

### FULL OUTER JOIN — all rows from both, NULLs where no match
```sql
SELECT users.name, departments.dept_name
FROM users
FULL OUTER JOIN departments ON users.department_id = departments.id;
```

### Visual mental model:
```
INNER  = only the overlap
LEFT   = all of left + overlap
RIGHT  = all of right + overlap
FULL   = everything from both
```

### Table aliases (you'll see these constantly):
```sql
SELECT u.name, d.dept_name
FROM users u                          -- "u" is an alias for "users"
JOIN departments d ON u.department_id = d.id;
```
Note: `JOIN` alone means `INNER JOIN`.

---

## Aggregate Functions — Summarising Data

```sql
SELECT COUNT(*) FROM users;                    -- Number of rows
SELECT COUNT(email) FROM users;                -- Number of non-NULL emails
SELECT COUNT(DISTINCT department_id) FROM users; -- Number of unique departments
SELECT SUM(salary) FROM users;                 -- Total
SELECT AVG(salary) FROM users;                 -- Average
SELECT MIN(age) FROM users;                    -- Smallest value
SELECT MAX(age) FROM users;                    -- Largest value
```

---

## GROUP BY — Aggregating by Category

```sql
-- How many users per department?
SELECT department_id, COUNT(*) AS user_count
FROM users
GROUP BY department_id;
```

**`AS` creates an alias** — renames the column in the output. `COUNT(*)` would otherwise have an ugly auto-generated name.

### HAVING — filtering groups (WHERE filters rows, HAVING filters groups):
```sql
-- Only departments with more than 5 users:
SELECT department_id, COUNT(*) AS user_count
FROM users
GROUP BY department_id
HAVING COUNT(*) > 5;
```

**Test favourite:** "What's the difference between WHERE and HAVING?"
- `WHERE` filters individual rows **before** grouping
- `HAVING` filters groups **after** grouping

---

## INSERT — Adding Data

```sql
-- Single row:
INSERT INTO users (name, age, email)
VALUES ('Andy', 35, 'andy@example.com');

-- Multiple rows:
INSERT INTO users (name, age)
VALUES ('Andy', 35), ('Laura', 33), ('Emily', 30);
```

---

## UPDATE — Changing Data

```sql
UPDATE users
SET email = 'new@example.com'
WHERE id = 1;                     -- ALWAYS include WHERE or you update every row

UPDATE users
SET age = age + 1                 -- Can use expressions
WHERE name = 'Andy';
```

**Danger:** `UPDATE users SET age = 0;` with no WHERE clause sets *every user's age to 0*. Same with DELETE.

---

## DELETE — Removing Data

```sql
DELETE FROM users WHERE id = 1;   -- Delete specific row(s)
DELETE FROM users;                -- Deletes ALL rows (table structure remains)
```

---

## Table Operations

### CREATE TABLE:
```sql
CREATE TABLE users (
    id INT PRIMARY KEY,                    -- Unique identifier, no duplicates
    name VARCHAR(100) NOT NULL,            -- String up to 100 chars, required
    email VARCHAR(255) UNIQUE,             -- Must be unique if provided
    age INT DEFAULT 0,                     -- Default value if not specified
    department_id INT,
    created_at TIMESTAMP DEFAULT NOW(),
    FOREIGN KEY (department_id) REFERENCES departments(id)  -- Links to another table
);
```

### Common data types:
| Type | What it stores |
|------|---------------|
| `INT` / `INTEGER` | Whole numbers |
| `FLOAT` / `DECIMAL` | Decimal numbers |
| `VARCHAR(n)` | Variable-length string up to n characters |
| `TEXT` | Unlimited-length string |
| `BOOLEAN` | True/False |
| `DATE` | Date only (2026-07-20) |
| `TIMESTAMP` | Date and time |
| `SERIAL` | Auto-incrementing integer (PostgreSQL) |
| `AUTO_INCREMENT` | Auto-incrementing integer (MySQL) |

### ALTER TABLE:
```sql
ALTER TABLE users ADD COLUMN phone VARCHAR(20);     -- Add column
ALTER TABLE users DROP COLUMN phone;                -- Remove column
ALTER TABLE users RENAME COLUMN name TO full_name;  -- Rename column
```

### DROP TABLE:
```sql
DROP TABLE users;                  -- Delete table entirely (structure and data)
DROP TABLE IF EXISTS users;        -- Only if it exists (prevents error)
```

---

## Constraints — Rules on Columns

| Constraint | Meaning |
|------------|---------|
| `PRIMARY KEY` | Unique identifier for each row. One per table. Never NULL. |
| `FOREIGN KEY` | References a PRIMARY KEY in another table. Creates a relationship. |
| `NOT NULL` | Column must have a value |
| `UNIQUE` | No duplicate values allowed |
| `DEFAULT value` | Used when no value is provided |
| `CHECK (condition)` | Custom validation (e.g., `CHECK (age >= 0)`) |

---

## Subqueries — Queries Inside Queries

```sql
-- Find users in the largest department:
SELECT name FROM users
WHERE department_id = (
    SELECT department_id FROM users
    GROUP BY department_id
    ORDER BY COUNT(*) DESC
    LIMIT 1
);

-- Find users whose salary is above average:
SELECT name, salary FROM users
WHERE salary > (SELECT AVG(salary) FROM users);

-- IN with subquery:
SELECT name FROM users
WHERE department_id IN (
    SELECT id FROM departments WHERE dept_name LIKE 'Eng%'
);
```

**How to read them:** Start with the inner query (in parentheses), understand what it returns, then read the outer query using that result.

---

## CASE — Conditional Logic (SQL's if/else)

```sql
SELECT name, age,
    CASE
        WHEN age < 18 THEN 'Minor'
        WHEN age < 65 THEN 'Adult'
        ELSE 'Senior'
    END AS age_group
FROM users;
```

---

## String Functions

```sql
UPPER('andy')              -- 'ANDY'
LOWER('ANDY')              -- 'andy'
LENGTH('Andy')             -- 4 (some databases: LEN)
CONCAT('Andy', ' ', 'B')  -- 'Andy B' (or use || in PostgreSQL)
SUBSTRING('Andy', 1, 3)   -- 'And' (start position, length) — NOTE: SQL is 1-indexed, not 0
TRIM('  Andy  ')           -- 'Andy'
REPLACE('Andy', 'A', 'B') -- 'Bndy'
```

---

## UNION — Combining Result Sets

```sql
-- Stack results from two queries (removes duplicates):
SELECT name FROM users
UNION
SELECT name FROM archived_users;

-- Keep duplicates:
SELECT name FROM users
UNION ALL
SELECT name FROM archived_users;
```

Both queries must have the **same number of columns** with **compatible types**.

---

## Common Gotchas (Test Favourites)

1. **NULL comparisons:** `WHERE age = NULL` is ALWAYS wrong. Use `IS NULL`. Any arithmetic with NULL produces NULL. `5 + NULL = NULL`.

2. **Single quotes for strings:** `WHERE name = 'Andy'` not `"Andy"`. Double quotes are for identifiers (column/table names) in most databases.

3. **GROUP BY requires all non-aggregated columns:**
   ```sql
   -- WRONG: name isn't aggregated or grouped
   SELECT name, COUNT(*) FROM users GROUP BY department_id;
   -- RIGHT:
   SELECT department_id, COUNT(*) FROM users GROUP BY department_id;
   ```

4. **WHERE vs HAVING:** WHERE filters rows before grouping. HAVING filters after. You cannot use aggregate functions in WHERE.

5. **ORDER BY DESC:** Default is `ASC` (ascending: A-Z, 0-9). Add `DESC` for descending. `ORDER BY age DESC`.

6. **DISTINCT:** `SELECT DISTINCT department_id FROM users;` — removes duplicate values from results.

7. **Execution order** (not the order you write it):
   `FROM` → `WHERE` → `GROUP BY` → `HAVING` → `SELECT` → `ORDER BY` → `LIMIT`
   This is why you can't use a column alias from SELECT in your WHERE clause.

8. **DELETE vs DROP vs TRUNCATE:**
   - `DELETE` — removes rows (can use WHERE, logged, slow)
   - `TRUNCATE` — removes all rows (no WHERE, fast, minimal logging)
   - `DROP` — removes the entire table (structure and data, gone)

9. **INNER JOIN excludes non-matching rows.** If you need everything from one side, use LEFT JOIN. This is the most commonly tested JOIN concept.

10. **COUNT(*) vs COUNT(column):** `COUNT(*)` counts all rows including NULLs. `COUNT(email)` counts only rows where email is not NULL.
