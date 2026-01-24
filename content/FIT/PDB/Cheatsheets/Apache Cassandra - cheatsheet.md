## Overview

**What is Cassandra?**

- Wide column store (column-family database)
- Open-source, high availability, linear scalability
- Peer-to-peer configurable replication (one, quorum, all)
- Tunable consistency, MapReduce support
- Developed by Apache (originally Facebook)
- Implemented in Java

**Structure Hierarchy:**

```
Instance → Keyspaces → Tables → Rows → Columns
```

---

## Data Model

### Column Family (Table)

- Collection of similar rows (rows don't need identical columns)
- Schema must be specified but can be modified later
- Each row uniquely identified by a **primary key**

### Row

- Collection of columns
- Associated with a unique row key
- Should encompass data accessed together

### Column

- Name-value pair + additional metadata (TTL, timestamp)

---

## Sample Data Visualization

**Table: actors**

|id|name|year|movies|
|---|---|---|---|
|'trojan'|('Ivan', 'Trojan')|1964|{'samotari', 'medvidek'}|
|'machacek'|('Jiří', 'Macháček')|1966|{'medvidek', 'vratnelahve', 'samotari'}|
|'sverak'|('Zdeněk', 'Svěrák')|1936|{'vratnelahve'}|

**Table: movies**

|id|title|year|director|actors|genres|
|---|---|---|---|---|---|
|'samotari'|'Samotáři'|2000|null|null|['comedy', 'drama']|
|'medvidek'|'Medvídek'|2007|('Jan', 'Hřebejk')|{'trojan': 'Ivan'}|null|

---

## Data Types

### Native Types

|Type|Description|Example|
|---|---|---|
|`TINYINT`, `SMALLINT`, `INT`, `BIGINT`|Signed integers (1B, 2B, 4B, 8B)|`42`|
|`VARINT`|Arbitrary-precision integer|`123456789012345`|
|`DECIMAL`|Variable-precision decimal|`3.14159`|
|`FLOAT`, `DOUBLE`|Floating point (4B, 8B)|`3.14`|
|`BOOLEAN`|Boolean values|`true`, `false`|
|`TEXT`, `VARCHAR`|UTF8 string (single quotes!)|`'hello'`|
|`ASCII`|ASCII string|`'ascii text'`|
|`DATE`|Date|`'2016-12-05'`|
|`TIME`|Time|`'09:15:00'`|
|`TIMESTAMP`|Date+time|`'2016-12-05 09:15:00'`|
|`COUNTER`|8B integer (increment/decrement only)|-|
|`BLOB`|Arbitrary bytes|-|
|`INET`|IP address (IPv4/IPv6)|-|

> ⚠️ **Counter Restrictions:**
> 
> - Cannot be part of primary key
> - Either ALL non-PK columns are counters, or NONE
> - TTL not supported
> - Value cannot be set directly (only increment/decrement)

### Tuple Type

**Declaration:**

```cql
TUPLE<TEXT, TEXT>
TUPLE<TEXT, INT, BOOLEAN>
```

**Literal:**

```cql
('Jiří', 'Macháček')
('value1', 42, true)
```

### Collection Types

**List** - ordered, allows duplicates:

```cql
-- Declaration
LIST<TEXT>

-- Literal
['comedy', 'drama']
['item1', 'item2', 'item1']  -- duplicates allowed
```

**Set** - unordered (internally sorted), unique values:

```cql
-- Declaration
SET<TEXT>

-- Literal
{'medvidek', 'vratnelahve', 'samotari'}
```

**Map** - key-value pairs, unique keys:

```cql
-- Declaration
MAP<TEXT, TEXT>

-- Literal
{'machacek': 'Robert Landa', 'sverak': 'Josef'}
```

### User-Defined Types (UDT)

```cql
-- Create UDT
CREATE TYPE details (
    length SMALLINT,
    annotation TEXT
);

-- Use in table
CREATE TABLE movies (
    id TEXT PRIMARY KEY,
    properties details
);

-- Literal
{length: 100, annotation: 'Great movie'}
```

---

## DDL Statements

### Keyspace Operations

```cql
-- Create keyspace (replication is MANDATORY)
CREATE KEYSPACE moviedb
WITH replication = {
    'class': 'SimpleStrategy',
    'replication_factor': 3
};

-- With NetworkTopologyStrategy (multiple data centers)
CREATE KEYSPACE moviedb
WITH replication = {
    'class': 'NetworkTopologyStrategy',
    'dc1': 3,
    'dc2': 2
};

-- Create only if not exists
CREATE KEYSPACE IF NOT EXISTS moviedb
WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 1};

-- Switch keyspace
USE moviedb;

-- Drop keyspace (removes all tables and data!)
DROP KEYSPACE moviedb;
DROP KEYSPACE IF EXISTS moviedb;

-- Alter keyspace options
ALTER KEYSPACE moviedb
WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 2};
```

### Table Operations

```cql
-- Create table with inline primary key
CREATE TABLE actors (
    id TEXT PRIMARY KEY,
    name TUPLE<TEXT, TEXT>,
    year SMALLINT,
    movies SET<TEXT>
);

-- Create table with separate primary key definition
CREATE TABLE movies (
    id TEXT,
    title TEXT,
    director TUPLE<TEXT, TEXT>,
    year SMALLINT,
    actors MAP<TEXT, TEXT>,
    genres LIST<TEXT>,
    countries SET<TEXT>,
    properties details,
    PRIMARY KEY (id)
);

-- Create only if not exists
CREATE TABLE IF NOT EXISTS actors (...);

-- Drop table
DROP TABLE movies;
DROP TABLE IF EXISTS movies;

-- Truncate (keep table, remove all data)
TRUNCATE TABLE movies;
TRUNCATE movies;

-- Alter table
ALTER TABLE movies ADD rating INT;
ALTER TABLE movies DROP rating;
```

### Primary Key Structure

**Components:**

1. **Partition Key** (mandatory) - determines data distribution across nodes
2. **Clustering Columns** (optional) - determines row ordering within partition

```cql
-- Single column partition key, no clustering
PRIMARY KEY (id)
-- Partition: id

-- Single partition key + clustering columns
PRIMARY KEY (country, city, street)
-- Partition: country | Clustering: city, street

-- Composite partition key
PRIMARY KEY ((country, region), city, street)
-- Partition: (country, region) | Clustering: city, street
```

**Examples:**

```cql
-- Simple: partition by user_id
CREATE TABLE user_posts (
    user_id TEXT,
    post_id TIMEUUID,
    content TEXT,
    PRIMARY KEY (user_id, post_id)
);
-- Rows with same user_id stored together, ordered by post_id

-- Composite partition key
CREATE TABLE sensor_data (
    sensor_id TEXT,
    date DATE,
    timestamp TIMESTAMP,
    value DOUBLE,
    PRIMARY KEY ((sensor_id, date), timestamp)
);
-- Data partitioned by sensor+date, ordered by timestamp
```

---

## DML Statements

### SELECT

**Basic Syntax:**

```cql
SELECT column1, column2
FROM table_name
WHERE condition
ORDER BY clustering_column
LIMIT n;
```

**Complete Example:**

```cql
SELECT id, title, actors
FROM movies
WHERE year = 2000 AND genres CONTAINS 'comedy';
```

#### SELECT Clause Options

```cql
-- All columns
SELECT * FROM movies;

-- Specific columns
SELECT id, title FROM movies;

-- With alias
SELECT id, title AS movie_title FROM movies;

-- Distinct values
SELECT DISTINCT year FROM movies;

-- Count rows
SELECT COUNT(*) FROM movies;

-- Metadata functions (NOT for collections!)
SELECT id, WRITETIME(title), TTL(title) FROM movies;
```

#### WHERE Clause Relations

|Operator|Description|Example|
|---|---|---|
|`=`|Equality|`WHERE id = 'test'`|
|`!=`|Not equal|`WHERE year != 2000`|
|`<`, `<=`, `>`, `>=`|Comparison|`WHERE year > 2000`|
|`IN`|Match any value|`WHERE id IN ('a', 'b')`|
|`CONTAINS`|Collection contains value|`WHERE genres CONTAINS 'drama'`|
|`CONTAINS KEY`|Map contains key|`WHERE actors CONTAINS KEY 'trojan'`|

```cql
-- Multiple conditions (AND only, no OR!)
SELECT * FROM movies
WHERE id = 'samotari' AND year = 2000;

-- Tuple comparison
SELECT * FROM table
WHERE (col1, col2) = ('val1', 'val2');
```

> ⚠️ **WHERE Clause Restrictions:**
> 
> - Only primary key columns unless secondary index exists
> - Non-equality on partition keys NOT supported
> - Conditions combined with `AND` only (no `OR`)

#### ORDER BY

```cql
-- Only clustering columns allowed!
SELECT * FROM user_posts
WHERE user_id = 'john'
ORDER BY post_id DESC;

-- Multiple ordering (must follow clustering order)
SELECT * FROM table
WHERE partition_key = 'x'
ORDER BY cluster1 ASC, cluster2 DESC;
```

#### LIMIT

```cql
SELECT * FROM movies LIMIT 10;
```

#### GROUP BY and Aggregates

```cql
-- Only primary key columns allowed for grouping!
SELECT year, COUNT(*) as movie_count
FROM movies
GROUP BY year;

-- Available aggregates
SELECT COUNT(title) FROM movies;      -- count non-null values
SELECT MIN(year), MAX(year) FROM movies;
SELECT SUM(rating), AVG(rating) FROM movies;
```

#### ALLOW FILTERING

```cql
-- Required for non-indexed, non-key column queries
-- ⚠️ WARNING: Can be very slow on large datasets!
SELECT * FROM movies
WHERE year = 2000
ALLOW FILTERING;
```

> ⚠️ **ALLOW FILTERING Warning:** By default, only queries where rows_read ≈ rows_returned are allowed. ALLOW FILTERING enables filtering queries but may have unpredictable performance!

---

### INSERT

```cql
-- Basic insert
INSERT INTO movies (id, title, year)
VALUES ('stesti', 'Štěstí', 2005);

-- Full insert with all types
INSERT INTO movies (id, title, director, year, actors, genres)
VALUES (
    'stesti',
    'Štěstí',
    ('Bohdan', 'Sláma'),               -- tuple
    2005,
    {'vilhelmova': 'Monika', 'liska': 'Toník'},  -- map
    ['comedy', 'drama']                 -- list
);

-- With TTL (time-to-live in seconds)
INSERT INTO movies (id, title, year)
VALUES ('temp', 'Temporary', 2020)
USING TTL 86400;  -- expires in 24 hours

-- With timestamp
INSERT INTO movies (id, title)
VALUES ('test', 'Test')
USING TIMESTAMP 1234567890;

-- Both TTL and timestamp
INSERT INTO movies (id, title)
VALUES ('test', 'Test')
USING TTL 3600 AND TIMESTAMP 1234567890;

-- Conditional insert (only if not exists)
INSERT INTO movies (id, title)
VALUES ('new', 'New Movie')
IF NOT EXISTS;
```

> 📝 **Note:** If row with same primary key exists, INSERT updates it (upsert behavior)

---

### UPDATE

```cql
-- Basic update
UPDATE movies
SET year = 2006, title = 'New Title'
WHERE id = 'vratnelahve';

-- Update with TTL
UPDATE movies
USING TTL 86400
SET title = 'Temporary Title'
WHERE id = 'test';

-- Update tuple
UPDATE movies
SET director = ('Jan', 'Svěrák')
WHERE id = 'vratnelahve';
```

#### Collection Updates

**Map operations:**

```cql
-- Replace entire map
UPDATE movies
SET actors = {'machacek': 'Robert', 'sverak': 'Josef'}
WHERE id = 'vratnelahve';

-- Add/update single entry
UPDATE movies
SET actors['vilhelmova'] = 'Helenka'
WHERE id = 'vratnelahve';

-- Add multiple entries
UPDATE movies
SET actors = actors + {'new_actor': 'Name'}
WHERE id = 'vratnelahve';

-- Remove entries by key
UPDATE movies
SET actors = actors - {'vilhelmova', 'landovsky'}
WHERE id = 'vratnelahve';
```

**List operations:**

```cql
-- Replace entire list
UPDATE movies
SET genres = ['comedy', 'drama']
WHERE id = 'vratnelahve';

-- Update by index (0-based!)
UPDATE movies
SET genres[1] = 'comedy'
WHERE id = 'vratnelahve';

-- Prepend to list
UPDATE movies
SET genres = ['drama'] + genres
WHERE id = 'vratnelahve';

-- Append to list
UPDATE movies
SET genres = genres + ['sci-fi']
WHERE id = 'vratnelahve';

-- Remove elements by value
UPDATE movies
SET genres = genres - ['drama', 'sci-fi']
WHERE id = 'vratnelahve';
```

**Set operations:**

```cql
-- Replace entire set
UPDATE movies
SET countries = {'CZ', 'SK'}
WHERE id = 'vratnelahve';

-- Add elements
UPDATE movies
SET countries = countries + {'DE', 'PL'}
WHERE id = 'vratnelahve';

-- Remove elements
UPDATE movies
SET countries = countries - {'SK'}
WHERE id = 'vratnelahve';
```

**UDT field updates:**

```cql
UPDATE movies
SET properties.length = 99
WHERE id = 'vratnelahve';
```

> 📝 **Note:** If row doesn't exist, UPDATE creates it (upsert behavior)

---

### DELETE

```cql
-- Delete entire row
DELETE FROM movies
WHERE id = 'vratnelahve';

-- Delete specific columns
DELETE title, year
FROM movies
WHERE id = 'vratnelahve';

-- Delete map entry
DELETE actors['machacek']
FROM movies
WHERE id = 'vratnelahve';

-- Delete list element by index
DELETE genres[0]
FROM movies
WHERE id = 'vratnelahve';

-- Delete UDT field
DELETE properties.length
FROM movies
WHERE id = 'vratnelahve';
```

---

## Additional Metadata

### TTL (Time-To-Live)

```cql
-- Set TTL on insert
INSERT INTO movies (id, title)
VALUES ('temp', 'Temporary')
USING TTL 3600;  -- 1 hour

-- Set TTL on update
UPDATE movies
USING TTL 7200
SET title = 'Updated'
WHERE id = 'temp';

-- Query remaining TTL
SELECT id, TTL(title) FROM movies WHERE id = 'temp';

-- Remove TTL (set to 0 or null)
UPDATE movies
USING TTL 0
SET title = 'Permanent'
WHERE id = 'temp';
```

### WRITETIME (Timestamp)

```cql
-- Set timestamp manually
INSERT INTO movies (id, title)
VALUES ('test', 'Test')
USING TIMESTAMP 1609459200000000;  -- microseconds!

-- Query writetime
SELECT id, WRITETIME(title) FROM movies WHERE id = 'test';
```

> ⚠️ **TTL/WRITETIME Limitations:**
> 
> - Cannot be used on collections
> - Cannot be used in WHERE clause
> - Cannot be used on primary key columns

---

## Common Patterns

### Time-Series Data

```cql
CREATE TABLE sensor_readings (
    sensor_id TEXT,
    day DATE,
    reading_time TIMESTAMP,
    value DOUBLE,
    PRIMARY KEY ((sensor_id, day), reading_time)
) WITH CLUSTERING ORDER BY (reading_time DESC);

-- Query latest readings for a sensor on a specific day
SELECT * FROM sensor_readings
WHERE sensor_id = 'temp_01' AND day = '2024-01-15'
LIMIT 100;
```

### User Activity Log

```cql
CREATE TABLE user_activity (
    user_id TEXT,
    activity_time TIMESTAMP,
    activity_type TEXT,
    details TEXT,
    PRIMARY KEY (user_id, activity_time)
) WITH CLUSTERING ORDER BY (activity_time DESC);

-- Get recent activities
SELECT * FROM user_activity
WHERE user_id = 'john'
ORDER BY activity_time DESC
LIMIT 20;
```

### Shopping Cart

```cql
CREATE TABLE shopping_cart (
    user_id TEXT,
    item_id TEXT,
    quantity INT,
    added_at TIMESTAMP,
    PRIMARY KEY (user_id, item_id)
);

-- Add/update item
UPDATE shopping_cart
SET quantity = 3, added_at = toTimestamp(now())
WHERE user_id = 'john' AND item_id = 'prod_123';

-- Remove item
DELETE FROM shopping_cart
WHERE user_id = 'john' AND item_id = 'prod_123';

-- Get entire cart
SELECT * FROM shopping_cart WHERE user_id = 'john';
```

---

## Quick Reference Card

### String Escaping

```cql
-- Single quotes for strings
'Hello World'

-- Escape single quote with double
'It''s a test'
```

### Null Values

```cql
-- Insert null
INSERT INTO movies (id, title, director)
VALUES ('test', 'Test', null);

-- Check for null is NOT directly supported in WHERE
-- Use IF EXISTS / IF NOT EXISTS for conditional operations
```

### Useful Functions

```cql
-- Current time
toTimestamp(now())
toDate(now())

-- UUID generation
uuid()
timeuuid()

-- Type conversion
toInt('123')
toString(123)
```

---

## Exam Tips

1. **Primary Key = Partition Key + Clustering Columns**
    - First element (or parenthesized group) = Partition key
    - Remaining elements = Clustering columns
2. **WHERE clause restrictions are strict**
    - Must include partition key
    - ORDER BY only on clustering columns
3. **INSERT and UPDATE both do upserts**
    - INSERT updates if exists
    - UPDATE creates if not exists
4. **Collections**
    - List: ordered, duplicates allowed, `[]`
    - Set: unique, internally sorted, `{}`
    - Map: key-value pairs, unique keys, `{key: value}`
5. **Counters are special**
    - Cannot set value directly
    - Only increment/decrement
    - Separate tables required
6. **TTL/WRITETIME don't work on:**
    - Collections
    - Primary key columns
    - In WHERE clauses