## Overview

**Neo4j** = Graph database using **property graphs**

- Nodes (vertices) + Relationships (edges)
- Both can have labels/types and properties
- Cypher = declarative query language with ASCII-art syntax

---

## Data Model

### Property Graph Components

|Component|Description|Example|
|---|---|---|
|**Node**|Entity with identity, labels, properties|`(:ACTOR {name: "Ivan Trojan"})`|
|**Relationship**|Directed edge with type and properties|`-[:PLAY {role: "Ondřej"}]->`|
|**Label**|Node category (0 or more per node)|`:MOVIE`, `:ACTOR`|
|**Type**|Relationship category (exactly 1)|`:PLAY`, `:KNOW`|
|**Property**|Key-value pair|`{year: 2006, rating: 76}`|

### Data Types

```cypher
-- Property types
"string"           -- String
42, 0xA5           -- Integer
3.14               -- Float
true, false        -- Boolean
null               -- Unknown/missing value

-- Composite types
[1, 2, "text"]                    -- List (ordered, heterogeneous)
{key: "value", num: 42}           -- Map (unordered key-value pairs)

-- Property maps (for nodes/relationships)
-- Only atomic values or homogeneous arrays allowed at top level
{title: "Samotáři", genres: ["comedy", "drama"]}
```

---

## Sample Data Structure

```cypher
-- Movies
(m1:MOVIE {id: "samotari", title: "Samotáři", year: 2000, rating: 84})
(m2:MOVIE {id: "medvidek", title: "Medvídek", year: 2007, rating: 53})

-- Actors  
(a1:ACTOR {id: "trojan", name: "Ivan Trojan", year: 1964})
(a2:ACTOR {id: "machacek", name: "Jiří Macháček", year: 1966})

-- Relationships
(m1)-[:PLAY {role: "Ondřej"}]->(a1)
(m1)-[:PLAY {role: "Jakub"}]->(a2)
```

---

## Path Patterns (ASCII-Art Syntax)

### Node Patterns

```cypher
()                              -- Any node
(n)                             -- Any node, bound to variable n
(:MOVIE)                        -- Node with label MOVIE
(m:MOVIE)                       -- Node with label, bound to m
(m:MOVIE {title: "Medvídek"})   -- Node with label and property
(m:MOVIE:CZECH)                 -- Node with multiple labels
```

### Relationship Patterns

```cypher
--                              -- Any direction, any type
-->                             -- Outgoing direction
<--                             -- Incoming direction
-[r]->                          -- Bound to variable r
-[:PLAY]->                      -- Specific type
-[:PLAY|:DIRECT]->              -- Multiple types (OR)
-[r:PLAY {role: "Jakub"}]->     -- Type with properties
-[*]->                          -- Variable length (any)
-[*2]->                         -- Exactly 2 hops
-[*1..3]->                      -- 1 to 3 hops
-[*..5]->                       -- Up to 5 hops
-[*2..]->                       -- At least 2 hops
```

### Complete Path Patterns

```cypher
(:MOVIE)-[:PLAY]->(:ACTOR)
(m:MOVIE)-[r:PLAY]->(a:ACTOR)
(a:ACTOR)<-[:PLAY]-(m:MOVIE)-[:PLAY]->(b:ACTOR)
```

---

## MATCH Clause

### Basic Matching

```cypher
-- Find node by property
MATCH (m:MOVIE {id: "medvidek"})
RETURN m, m.title;

-- Equivalent with WHERE
MATCH (m:MOVIE)
WHERE m.id = "medvidek"
RETURN m, m.title;
```

### Pattern Matching

```cypher
-- Find movies where specific actor played
MATCH (:ACTOR {name: "Jiri Machacek"})<-[:PLAY]-(n:MOVIE)
RETURN n.title;

-- Alternative directions (all equivalent)
MATCH (n:MOVIE)-[:PLAY]->(:ACTOR {name: "Jiri Machacek"})
RETURN n.title;

-- Ignoring direction
MATCH (:ACTOR {name: "Jiri Machacek"})--(n:MOVIE)
RETURN n.title;
```

### Multiple MATCH Clauses

```cypher
-- Separate MATCH clauses (joined)
MATCH (a:ACTOR {name: "Jiri Machacek"})
MATCH (n:MOVIE)-[:PLAY]->(a)
RETURN n.title;

-- Combined in single MATCH
MATCH (a:ACTOR {name: "Jiri Machacek"}), (n:MOVIE)-[:PLAY]->(a)
RETURN n.title;
```

---

## Finding Movies with Actors

### Movies that have at least one actor

```cypher
-- Using DISTINCT (removes duplicate movies)
MATCH (m:MOVIE)-[:PLAY]->(:ACTOR)
RETURN DISTINCT m;

-- Using pattern comprehension with SIZE
MATCH (m:MOVIE)
WHERE SIZE([(m)-[:PLAY]->(a:ACTOR) | a]) >= 1
RETURN m;

-- Using EXISTS
MATCH (m:MOVIE)
WHERE EXISTS((m)-[:PLAY]->(:ACTOR))
RETURN m;

-- Using pattern as condition (shorthand for EXISTS)
MATCH (m:MOVIE)
WHERE (m)-[:PLAY]->(:ACTOR)
RETURN m;

-- Using WITH and COUNT
MATCH (m:MOVIE)-[:PLAY]->(a:ACTOR)
WITH m, COUNT(a) as actors
WHERE actors >= 1
RETURN m;

-- Note: This version also works (COUNT filters nulls)
MATCH (m:MOVIE)-[:PLAY]->(a:ACTOR)
WITH m, COUNT(a) as actors
RETURN m;
```

---

## Co-actors Query (Actors who played with someone)

```cypher
-- Find actors who played with Ivan Trojan
MATCH
  (s:ACTOR {name: "Ivan Trojan"})
    <-[:PLAY]-(m:MOVIE)-[:PLAY]->
  (a:ACTOR)
RETURN DISTINCT a;

-- Alternative with separate patterns
MATCH (s:ACTOR {name: "Ivan Trojan"})<-[:PLAY]-(m:MOVIE),
      (m)-[:PLAY]->(a:ACTOR)
RETURN DISTINCT a;

-- Excluding the original actor
MATCH (s:ACTOR {name: "Ivan Trojan"})<-[:PLAY]-(m:MOVIE)
MATCH (m)-[:PLAY]->(a:ACTOR)
WHERE a <> s
RETURN DISTINCT a;

-- Alternative: compare by name
-- ... WHERE a.name <> "Ivan Trojan"

-- Using subquery pattern
MATCH (a:ACTOR)
WHERE (a)<-[:PLAY]-(:MOVIE)-[:PLAY]->(:ACTOR {name: "Ivan Trojan"})
RETURN a;
```

> **Important**: Relationship uniqueness is enforced within a single MATCH pattern, but NOT across multiple MATCH clauses. Use `WHERE a <> s` to exclude the same node.

---

## Variable-Length Paths

```cypher
-- Find all actors connected through KNOW relationship
MATCH (s:ACTOR {name: "Ivan Trojan"})-[:KNOW*]-(a:ACTOR)
WHERE s <> a
RETURN DISTINCT a.name;

-- Explicit: at least 1 hop
MATCH (s:ACTOR {name: "Ivan Trojan"})-[:KNOW*1..]-(a:ACTOR)
WHERE s <> a
RETURN DISTINCT a.name;

-- Using EXISTS
MATCH (a:ACTOR)
WHERE EXISTS((a)-[:KNOW*]-(:ACTOR {name: "Ivan Trojan"}))
  AND a.name <> "Ivan Trojan"
RETURN a.name;
```

---

## OPTIONAL MATCH

Works like LEFT OUTER JOIN - returns `null` for unmatched patterns.

```cypher
-- All movies with their actors (including movies without actors)
MATCH (m:MOVIE)
OPTIONAL MATCH (m)-[:PLAY]->(a:ACTOR)
RETURN m.title, a.name;
```

**Result includes movies without actors** (with `null` for actor).

---

## WHERE Conditions

### Comparison Operators

```cypher
=, <>, <, <=, >, >=

-- Chained comparisons
WHERE 2015 <= m.year < 2020
-- Equivalent to: WHERE 2015 <= m.year AND m.year < 2020
```

### NULL Testing

```cypher
WHERE m.director IS NULL
WHERE m.director IS NOT NULL
```

### String Matching

```cypher
WHERE m.title STARTS WITH "Med"
WHERE m.title CONTAINS "vid"
WHERE m.title ENDS WITH "ek"
WHERE m.title =~ ".*Bobule"   -- Regular expression
```

### List Operations

```cypher
WHERE m.language IN ["cs", "sk"]
WHERE "comedy" IN m.genres
```

### Pattern Conditions

```cypher
-- Path exists
WHERE (m)-[:PLAY]->(:ACTOR)

-- Existential subquery
WHERE EXISTS { (m)-[:PLAY]->(a:ACTOR) WHERE a.name = "Ivan Trojan" }
```

### Quantifiers

```cypher
WHERE ALL(g IN m.genres WHERE g <> "horror")
WHERE ANY(g IN m.genres WHERE g = "comedy")
WHERE NONE(g IN m.genres WHERE g = "horror")
WHERE SINGLE(g IN m.genres WHERE g = "drama")
```

### Logical Operators

```cypher
AND, OR, NOT
WHERE (m.year > 2000) AND (m.rating >= 75)
WHERE NOT (m)-[:PLAY]->(:ACTOR)
```

---

## RETURN Clause

### Basic Projection

```cypher
RETURN m                    -- Whole node
RETURN m.title, m.year      -- Properties
RETURN m.title AS name      -- Alias
RETURN *                    -- All variables
RETURN DISTINCT m.title     -- Remove duplicates
```

### ORDER BY

```cypher
RETURN a.name, a.year 
ORDER BY a.year DESC, a.name ASC;

-- Alternative keywords
ORDER BY a.year DESCENDING, a.name ASCENDING;

-- Default is ASC
ORDER BY a.year DESC, a.name;
```

### Pagination

```cypher
RETURN m.title
ORDER BY m.year
SKIP 10
LIMIT 5;
```

---

## WITH Clause

Creates intermediate result for chaining. Like RETURN but continues query.

```cypher
-- Movies with above-average number of actors
MATCH (m:MOVIE)
WITH m, SIZE([(m)-[:PLAY]->(a:ACTOR) | a]) AS actors
WITH AVG(actors) AS average
MATCH (m:MOVIE)
WHERE SIZE([(m)-[:PLAY]->(a:ACTOR) | a]) > average
RETURN m.title;
```

### WITH ... WHERE (Filtering after aggregation)

```cypher
MATCH (m:MOVIE)-[:PLAY]->(a:ACTOR)
WITH m, COUNT(a) as actorCount
WHERE actorCount > 2
RETURN m.title, actorCount;
```

---

## Aggregation

### Aggregate Functions

```cypher
COUNT(*)              -- Count all rows
COUNT(x)              -- Count non-null values
COUNT(DISTINCT x)     -- Count distinct values
SUM(x), AVG(x)        -- Numeric aggregation
MIN(x), MAX(x)        -- Min/max values
COLLECT(x)            -- Collect into list
COLLECT(DISTINCT x)   -- Collect distinct values
```

### Implicit Grouping

```cypher
-- All non-aggregated columns become grouping columns
MATCH (a:ACTOR)<-[:PLAY]-(m:MOVIE)
WHERE a.year >= 1965
RETURN a.name, COUNT(m) AS count, COLLECT(m.title) AS movies;

-- Result groups by a.name automatically
```

### Actors with movie count (including those with 0 movies)

```cypher
MATCH (a:ACTOR)
WHERE a.year >= 1965
OPTIONAL MATCH (a)<-[:PLAY]-(m:MOVIE)
RETURN a.name, COUNT(m) AS count, COLLECT(m.title) AS movies;
```

---

## Pattern Comprehension

### List Comprehension

```cypher
[i IN range(1, 5) WHERE i % 2 = 0]           -- [2, 4]
[i IN range(1, 5) WHERE i % 2 = 0 | i * 10]  -- [20, 40]
```

### Pattern Comprehension

```cypher
-- Count actors for a movie
SIZE([(m)-[:PLAY]->(a:ACTOR) | a])

-- Get movie titles for an actor
[(a)<-[:PLAY]-(m:MOVIE) | m.title]

-- With filtering
[(m:MOVIE)-[:PLAY]->(a:ACTOR) WHERE m.year >= 2005 | m.title]
```

---

## List Operations

### Access and Slicing

```cypher
m.genres[0]           -- First element
m.genres[-1]          -- Last element
range(1,5)[1..3]      -- [2, 3] (upper bound exclusive)
range(1,5)[..3]       -- [1, 2, 3]
range(1,5)[2..]       -- [3, 4, 5]
range(1,5)[-3..-1]    -- [3, 4]
```

### Property Access

```cypher
m.title               -- Static lookup
m["title"]            -- Dynamic lookup
m["property" + num]   -- Computed key
```

---

## Actors Playing in Movies with Above-Average Actor Count

```cypher
MATCH (m:MOVIE)
WITH m, SIZE([(m)-[:PLAY]->(a:ACTOR) | a]) AS actors
WITH AVG(actors) AS average
MATCH (m:MOVIE)
WHERE SIZE([(m)-[:PLAY]->(a:ACTOR) | a]) > average
MATCH (m)-[:PLAY]->(a:ACTOR)
WITH DISTINCT a
RETURN a.name;

-- Alternative with OPTIONAL MATCH for accurate average
MATCH (m:MOVIE)
OPTIONAL MATCH (m)-[:PLAY]->(a:ACTOR)
WITH m, COUNT(a) AS actors
WITH AVG(actors) AS average
MATCH (m:MOVIE)
WHERE SIZE([(m)-[:PLAY]->(a:ACTOR) | a]) > average
MATCH (m)-[:PLAY]->(a:ACTOR)
WITH DISTINCT a
RETURN a.name;
```

---

## UNION

```cypher
MATCH (m:MOVIE) WHERE m.year < 2000
RETURN m.title AS name
UNION
MATCH (a:ACTOR) WHERE a.year < 1970
RETURN a.name AS name;

-- UNION ALL keeps duplicates
MATCH ... RETURN ...
UNION ALL
MATCH ... RETURN ...;
```

> **Note**: Column names must match across all parts.

---

## Query Structure Rules

```
[MATCH/UNWIND]* → [CREATE/MERGE/DELETE/SET/REMOVE]* → [WITH → repeat] → RETURN
```

1. Read clauses (MATCH) must precede write clauses within a query part
2. WITH separates query parts
3. RETURN must be last (unless query has write clauses)
4. Read-only queries must end with RETURN

---

## Common Patterns & Idioms

### Check if relationship/node exists

```cypher
-- Node has at least one relationship of type
WHERE (n)-[:TYPE]->()

-- Using EXISTS
WHERE EXISTS((n)-[:TYPE]->())

-- Using SIZE
WHERE SIZE([(n)-[:TYPE]->(x) | x]) > 0
```

### Conditional return (first label if exists)

```cypher
-- Similar to COALESCE
RETURN COALESCE(m.label, "default")
```

### Finding nodes without relationships

```cypher
MATCH (m:MOVIE)
WHERE NOT (m)-[:PLAY]->()
RETURN m;
```

---

## Common Mistakes

### ❌ Forgetting DISTINCT with multiple paths

```cypher
-- May return duplicate actors
MATCH (m:MOVIE)-[:PLAY]->(a:ACTOR)
RETURN a.name;

-- ✅ Correct
MATCH (m:MOVIE)-[:PLAY]->(a:ACTOR)
RETURN DISTINCT a.name;
```

### ❌ Expecting relationship uniqueness across MATCH clauses

```cypher
-- Person can appear in both positions!
MATCH (s:ACTOR)<-[:PLAY]-(m:MOVIE)
MATCH (m)-[:PLAY]->(a:ACTOR)
RETURN a;  -- May include s!

-- ✅ Correct: explicitly exclude
MATCH (s:ACTOR {name: "X"})<-[:PLAY]-(m:MOVIE)
MATCH (m)-[:PLAY]->(a:ACTOR)
WHERE a <> s
RETURN a;
```

### ❌ Using aggregation without understanding grouping

```cypher
-- This groups by a.name automatically
RETURN a.name, COUNT(m)

-- If you want total count, don't include other columns
RETURN COUNT(m)
```

---

## Quick Reference: Keywords

|Keyword|Purpose|
|---|---|
|`MATCH`|Find patterns in graph|
|`OPTIONAL MATCH`|Match or return null|
|`WHERE`|Filter results|
|`RETURN`|Output results|
|`WITH`|Intermediate results|
|`ORDER BY`|Sort results|
|`SKIP`, `LIMIT`|Pagination|
|`DISTINCT`|Remove duplicates|
|`AS`|Alias|
|`UNION`|Combine results|
|`CREATE`|Create nodes/relationships|
|`DELETE`|Delete nodes/relationships|
|`SET`|Set properties/labels|
|`REMOVE`|Remove properties/labels|

---

## Sample Exam Question Pattern

**Question**: Construct XHTML table with movies, filtering and sorting.

**Approach**:

1. MATCH the required pattern
2. WHERE for filtering conditions
3. WITH for intermediate calculations if needed
4. RETURN with required columns
5. ORDER BY for sorting
6. Use aggregation functions as needed

```cypher
-- Example: Sessions without adult tickets, sorted
MATCH (s:SESSION)
WHERE NOT EXISTS {
  MATCH (s)-[:HAS]->(t:TICKET {type: "adult"})
}
RETURN s.movie, 
       COALESCE(s.labels[0], "Standard") AS label,
       SIZE([(s)-[:HAS]->(t:TICKET) | t]) AS tickets
ORDER BY s.length ASC, s.movie DESC;
```