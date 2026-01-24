# SPARQL Cheatsheet

## Sample Data (RDF Turtle)

```turtle
@prefix i: <http://db.cz/terms#> .
@prefix m: <http://db.cz/movies/> .
@prefix a: <http://db.cz/actors/> .

m:vratnelahve
  rdf:type i:Movie ;
  i:title "Vratné lahve" ;
  i:year 2006 ;
  i:actor a:sverak , a:machacek .

m:samotari
  rdf:type i:Movie ;
  i:title "Samotáři" ;
  i:year 2000 ;
  i:actor a:schneiderova , a:trojan , a:machacek .

m:medvidek
  rdf:type i:Movie ;
  i:title "Medvídek" ;
  i:year 2007 ;
  i:actor a:machacek , a:trojan ;
  i:director "Jan Hřebejk" .

m:zelary rdf:type i:Movie .

a:trojan
  rdf:type i:Actor ;
  i:firstname "Ivan" ; i:lastname "Trojan" ;
  i:year 1964 .

a:machacek
  rdf:type i:Actor ;
  i:firstname "Jiří" ; i:lastname "Macháček" ;
  i:year 1966 .
```

---

## Query Structure

```sparql
PREFIX prefix: <IRI>          # declare prefix (can have multiple)
BASE <IRI>                    # base IRI for relative references

SELECT ...                    # variables to return
FROM <graph>                  # data graph (default graph)
FROM NAMED <graph>            # named graph
WHERE { ... }                 # graph patterns
GROUP BY ...                  # grouping
HAVING (...)                  # group filter
ORDER BY ...                  # sorting
LIMIT n                       # max results
OFFSET n                      # skip results
```

---

## Prologue Declarations

```sparql
# PREFIX - multiple allowed, creates shortcuts
PREFIX i: <http://db.cz/terms#>
PREFIX m: <http://db.cz/movies/>
PREFIX : <http://default.org/>         # empty prefix

# BASE - at most one, for relative IRIs
BASE <http://db.cz/>

# Usage
i:Movie                  # expands to <http://db.cz/terms#Movie>
m:samotari               # expands to <http://db.cz/movies/samotari>
:something               # expands to <http://default.org/something>
```

---

## Variables

```sparql
?variable                # standard variable syntax
$variable                # alternative syntax (equivalent)

# Both are identical:
SELECT ?name ?age
SELECT $name $age
```

---

## Triple Patterns

### Basic Triples

```sparql
# Full form - separated by .
?m rdf:type i:Movie .
?m i:title ?t .
?m i:year ?y .

# Predicate-object list with ; (same subject)
?m rdf:type i:Movie ;
   i:title ?t ;
   i:year ?y .

# Object list with , (same subject and predicate)
?m i:actor a:trojan , a:machacek .
# Equivalent to:
?m i:actor a:trojan .
?m i:actor a:machacek .
```

### Blank Nodes

```sparql
# Anonymous blank node - acts as non-selectable variable
?s i:name [ i:first ?f ; i:last ?l ] .

# Equivalent explicit forms:
?s i:name ?b .
?b i:first ?f ; i:last ?l .

# Or with labeled blank node:
?s i:name _:b .
_:b i:first ?f ; i:last ?l .

# Blank node as subject (less common)
[ rdf:type i:Course ; i:code ?c ; i:title ?t ]
[] rdf:type i:Course ; i:code ?c ; i:title ?t .
_:b rdf:type i:Course ; i:code ?c ; i:title ?t .
```

---

## Graph Patterns

### Basic Graph Pattern

```sparql
# All triples must match (conjunction/AND)
WHERE {
  ?m rdf:type i:Movie ;
     i:title ?t ;
     i:year ?y .
}
```

### Group Graph Pattern

```sparql
# Enclosed in {} - can contain nested patterns
WHERE {
  ?m rdf:type i:Movie .
  { ?m i:actor ?a }       # nested group
}
```

### OPTIONAL Pattern

```sparql
# Match if possible, don't eliminate solution if not
WHERE {
  ?m rdf:type i:Movie ;
     i:title ?t .
  OPTIONAL { ?m i:director ?d }   # ?d unbound if no director
}

# Multiple independent OPTIONALs
WHERE {
  ?s rdf:type i:Student ;
     i:number ?n .
  OPTIONAL { ?s i:email ?e }      # independent
  OPTIONAL { ?s i:web ?w }        # independent
}

# WRONG - nested OPTIONAL (different semantics!)
WHERE {
  ?s rdf:type i:Student .
  OPTIONAL {
    ?s i:email ?e
    OPTIONAL { ?s i:web ?w }      # web only checked if email exists
  }
}

# WRONG - combined in one OPTIONAL (both must match or neither)
WHERE {
  ?s rdf:type i:Student .
  OPTIONAL { ?s i:email ?e ; i:web ?w }  # both or nothing
}
```

### UNION Pattern

```sparql
# Either pattern matches (disjunction/OR)
WHERE {
  { ?m i:actor a:trojan } 
  UNION 
  { ?m i:actor a:machacek }
}

# Alternative approach with FILTER
WHERE {
  ?m i:actor ?a .
  FILTER ( ?a = a:trojan || ?a = a:machacek )
}
```

### MINUS Pattern

```sparql
# Remove compatible solutions
WHERE {
  ?m rdf:type i:Movie ;
     i:title ?t .
  MINUS { ?m i:director ?d }      # movies without director
}
```

### FILTER NOT EXISTS

```sparql
# More flexible negation than MINUS
WHERE {
  ?p rdf:type i:Course ; i:code ?c .
  FILTER NOT EXISTS {
    ?e i:course ?p ; 
       i:day ?d ; 
       i:semester s:semester251 .
    FILTER ( ?d = "MON" || ?d = "FRI" )
  }
}
```

### NOT EXISTS vs MINUS

```sparql
# Both find courses NOT taught on MON/FRI:

# Using FILTER NOT EXISTS
WHERE {
  ?p rdf:type i:Course ; i:code ?c .
  FILTER NOT EXISTS {
    ?e i:course ?p ; i:day ?d ; i:semester s:semester251 .
    FILTER ( ?d = "MON" || ?d = "FRI" )
  }
}

# Using MINUS
WHERE {
  ?p rdf:type i:Course ; i:code ?c .
  MINUS {
    ?e i:course ?p ; i:day ?d ; i:semester s:semester251 .
    FILTER ( ?d = "MON" || ?d = "FRI" )
  }
}

# WRONG approach - only filters existing enrollments, misses courses with no enrollment
WHERE {
  ?p rdf:type i:Course ; i:code ?c .
  ?e i:course ?p ; i:day ?d ; i:semester s:semester251 .
  FILTER ( ?d != "MON" && ?d != "FRI" )
}
```

### Negation via OPTIONAL + !BOUND

```sparql
# Alternative to NOT EXISTS/MINUS
WHERE {
  ?p rdf:type i:Course ; i:code ?c .
  OPTIONAL {
    ?e i:course ?p ; i:day ?d ; i:semester s:semester251 .
    FILTER ( ?d = "MON" || ?d = "FRI" )
  }
  FILTER ( !BOUND(?e) )           # keep only if ?e not bound
}

# Multiple conditions
WHERE {
  ?p rdf:type i:Course ; i:code ?c .
  OPTIONAL { ?e1 i:course ?p ; i:day "MON" ; i:semester s:semester251 }
  OPTIONAL { ?e2 i:course ?p ; i:day "FRI" ; i:semester s:semester251 }
  FILTER ( !BOUND(?e1) && !BOUND(?e2) )
}
```

### GRAPH Pattern

```sparql
# Query specific named graph
FROM <http://db.cz/movies>
FROM NAMED <http://db.cz/actors>
WHERE {
  m:medvidek i:actor ?a .              # from default graph
  GRAPH <http://db.cz/actors> {        # from named graph
    ?a i:firstname ?f ; i:lastname ?l .
  }
}

# Query all named graphs
WHERE {
  GRAPH ?g { ?s ?p ?o }                # ?g binds to graph IRI
}
```

### BIND

```sparql
# Assign computed value to new variable
WHERE {
  ?a i:firstname ?f ; i:lastname ?l .
  BIND ( CONCAT(?f, " ", ?l) AS ?fullname )
}

# Variable must not already be bound!
```

---

## FILTER Constraints

### Comparison Operators

```sparql
=, !=, <, <=, >=, >

FILTER ( ?year >= 2005 )
FILTER ( ?year != 2000 )

# Ordering: unbound < blank node < IRI < literal
```

### Logical Operators

```sparql
&&                       # AND
||                       # OR
!                        # NOT

FILTER ( ?year >= 2005 && ?year <= 2010 )
FILTER ( ?day = "MON" || ?day = "FRI" )
FILTER ( !(?year < 2000) )

# 3-value logic: true, false, error (for unbound)
```

### Arithmetic Operators

```sparql
+, -, *, /

FILTER ( ?price * ?quantity > 100 )
BIND ( ?price * 1.21 AS ?priceWithVAT )
```

### Set Membership

```sparql
IN (value1, value2, ...)
NOT IN (value1, value2, ...)

FILTER ( ?day IN ("MON", "FRI") )
FILTER ( ?status NOT IN ("deleted", "archived") )
```

### String Functions

```sparql
REGEX(?string, "pattern")            # regex match
REGEX(?string, "pattern", "i")       # case-insensitive

FILTER ( REGEX(?code, "^NPRG") )     # starts with NPRG
FILTER ( REGEX(?name, "son$", "i") ) # ends with son (case-insensitive)

STR(?term)                           # lexical form of IRI or literal
STRLEN(?str)                         # string length
SUBSTR(?str, start, length)          # substring (1-indexed)
UCASE(?str)                          # uppercase
LCASE(?str)                          # lowercase
CONTAINS(?str, "sub")                # contains substring
STRSTARTS(?str, "prefix")            # starts with
STRENDS(?str, "suffix")              # ends with
CONCAT(?str1, " ", ?str2)            # concatenation
REPLACE(?str, "old", "new")          # replace
```

### Type/Term Tests

```sparql
BOUND(?var)              # true if variable is bound
!BOUND(?var)             # true if variable is NOT bound

isIRI(?term)             # true if IRI
isBLANK(?term)           # true if blank node
isLITERAL(?term)         # true if literal

LANG(?literal)           # language tag
DATATYPE(?literal)       # datatype IRI
```

### Existence Tests

```sparql
EXISTS { pattern }       # true if pattern matches at least once
NOT EXISTS { pattern }   # true if pattern doesn't match

FILTER EXISTS { ?m i:actor a:trojan }
FILTER NOT EXISTS { ?m i:director ?d }
```

### Conditional

```sparql
IF(condition, then_value, else_value)

BIND ( IF(?score >= 50, "pass", "fail") AS ?result )
```

### COALESCE

```sparql
COALESCE(expr1, expr2, ...)    # first non-error value

BIND ( COALESCE(?nickname, ?firstname, "Unknown") AS ?displayName )
```

---

## SELECT Clause

```sparql
# Select specific variables
SELECT ?title ?year

# Select all variables
SELECT *

# Computed expressions with alias
SELECT ?title (?year + 1 AS ?nextYear)
SELECT (CONCAT(?first, " ", ?last) AS ?fullName)

# Aggregates
SELECT ?director (COUNT(?m) AS ?movieCount)

# Remove duplicates
SELECT DISTINCT ?actor

# May remove some duplicates (implementation-dependent)
SELECT REDUCED ?actor
```

---

## FROM Clause

```sparql
# Default graph (merged if multiple)
FROM <http://db.cz/movies>
FROM <http://db.cz/actors>        # merged into default graph

# Named graphs (for GRAPH pattern)
FROM NAMED <http://db.cz/movies>
FROM NAMED <http://db.cz/actors>

# Combined
FROM <http://db.cz/movies>
FROM NAMED <http://db.cz/actors>
```

---

## Solution Modifiers

### ORDER BY

```sparql
ORDER BY ?year                    # ascending (default)
ORDER BY ASC(?year)               # ascending (explicit)
ORDER BY DESC(?year)              # descending

# Multiple criteria
ORDER BY DESC(?year) ASC(?title)

# By expression
ORDER BY STRLEN(?title)
```

### LIMIT and OFFSET

```sparql
LIMIT 10                          # return at most 10 results
OFFSET 5                          # skip first 5 results

# Pagination
ORDER BY ?id
LIMIT 10
OFFSET 20                         # page 3 (results 21-30)
```

---

## Aggregation

### GROUP BY

```sparql
# Group by one or more variables
SELECT ?director (COUNT(?m) AS ?count)
WHERE { ?m i:director ?director }
GROUP BY ?director

# Must include all non-aggregated SELECT variables
SELECT ?director ?year (COUNT(?m) AS ?count)
WHERE { ?m i:director ?director ; i:year ?year }
GROUP BY ?director ?year

# Group by expression
GROUP BY (YEAR(?date) AS ?year)
```

### HAVING

```sparql
# Filter groups (like WHERE but for aggregated results)
SELECT ?director (COUNT(?m) AS ?count)
WHERE { ?m i:director ?director }
GROUP BY ?director
HAVING (COUNT(?m) >= 3)

SELECT ?s ?f ?l (AVG(?r) AS ?avg)
WHERE { ... }
GROUP BY ?s ?f ?l
HAVING (COUNT(?c) <= 10)
```

### Aggregate Functions

```sparql
COUNT(?var)              # count values
COUNT(*)                 # count solutions
COUNT(DISTINCT ?var)     # count unique values

SUM(?var)                # sum
AVG(?var)                # average
MIN(?var)                # minimum
MAX(?var)                # maximum

GROUP_CONCAT(?var; SEPARATOR=", ")   # concatenate values
GROUP_CONCAT(DISTINCT ?var; SEPARATOR=", ")

SAMPLE(?var)             # arbitrary value from group
```

---

## Query Forms

### SELECT

```sparql
# Returns table of variable bindings
SELECT ?title ?year
WHERE { ?m i:title ?title ; i:year ?year }
```

### ASK

```sparql
# Returns boolean (true if any match exists)
ASK WHERE { ?m i:director "Jan Hřebejk" }
```

### CONSTRUCT

```sparql
# Returns new RDF graph
CONSTRUCT {
  ?a i:fullname ?name .
}
WHERE {
  ?a i:firstname ?f ; i:lastname ?l .
  BIND ( CONCAT(?f, " ", ?l) AS ?name )
}
```

### DESCRIBE

```sparql
# Returns implementation-defined description of resources
DESCRIBE <http://db.cz/movies/samotari>
DESCRIBE ?m WHERE { ?m i:year 2007 }
```

---

## Common Patterns

### Pattern 1: Basic Select with Filter

```sparql
PREFIX i: <http://db.cz/terms#>
SELECT ?title ?year
FROM <http://db.cz/movies>
WHERE {
  ?m rdf:type i:Movie ;
     i:title ?title ;
     i:year ?year .
  FILTER ( ?year >= 2005 )
}
ORDER BY ?year
```

### Pattern 2: Optional Properties

```sparql
SELECT ?n ?email ?web
WHERE {
  ?s rdf:type i:Student ; i:number ?n .
  OPTIONAL { ?s i:email ?email }
  OPTIONAL { ?s i:web ?web }
}
```

### Pattern 3: Filtering with REGEX

```sparql
SELECT ?code ?title
WHERE {
  ?p rdf:type i:Course ;
     i:code ?code ;
     i:title ?title .
  FILTER ( REGEX(?code, "^NPRG") )
}
ORDER BY ?title
```

### Pattern 4: UNION for OR conditions

```sparql
SELECT DISTINCT ?p ?c
WHERE {
  ?p rdf:type i:Course ; i:code ?c .
  {
    ?e i:course ?p ; i:day "MON" ; i:semester s:semester251
  } UNION {
    ?e i:course ?p ; i:day "FRI" ; i:semester s:semester251
  }
}
```

### Pattern 5: Negation with NOT EXISTS

```sparql
SELECT ?p ?c
WHERE {
  ?p rdf:type i:Course ; i:code ?c .
  FILTER NOT EXISTS {
    ?e i:course ?p ; i:day ?d ; i:semester s:semester251 .
    FILTER ( ?d = "MON" || ?d = "FRI" )
  }
}
```

### Pattern 6: Negation with OPTIONAL + !BOUND

```sparql
SELECT ?p ?c
WHERE {
  ?p rdf:type i:Course ; i:code ?c .
  OPTIONAL {
    ?e i:course ?p ; i:day ?d ; i:semester s:semester251 .
    FILTER ( ?d = "MON" || ?d = "FRI" )
  }
  FILTER ( !BOUND(?e) )
}
```

### Pattern 7: Aggregation with HAVING

```sparql
SELECT ?title (COUNT(?a) AS ?actorCount)
WHERE {
  ?m rdf:type i:Movie ;
     i:title ?title ;
     i:actor ?a .
}
GROUP BY ?m ?title
HAVING (COUNT(?a) <= 2)
ORDER BY ?actorCount ?title
```

### Pattern 8: Nested Blank Nodes

```sparql
SELECT ?f ?l (AVG(?r) AS ?avg)
WHERE {
  ?s rdf:type i:Student ;
     i:name [ i:first ?f ; i:last ?l ] ;
     i:enroll [ i:course ?c ; i:result ?r ; i:semester s:semester251 ] .
}
GROUP BY ?s ?f ?l
HAVING (COUNT(?c) <= 10)
```

### Pattern 9: Join Across Graphs

```sparql
PREFIX i: <http://db.cz/terms#>
SELECT ?f ?l
FROM <http://db.cz/movies>
FROM <http://db.cz/actors>
WHERE {
  m:medvidek i:actor ?a .
  ?a i:firstname ?f ; i:lastname ?l .
}
```

### Pattern 10: CONSTRUCT New Triples

```sparql
CONSTRUCT {
  ?a i:name ?fullname .
}
WHERE {
  ?a rdf:type i:Actor ;
     i:firstname ?f ;
     i:lastname ?l .
  BIND ( CONCAT(?f, " ", ?l) AS ?fullname )
}
```

---

## Functions Reference

### String Functions

|Function|Description|Example|
|---|---|---|
|`STR(?x)`|Convert to string|`STR(<http://ex.org>)` → `"http://ex.org"`|
|`STRLEN(?s)`|String length|`STRLEN("hello")` → `5`|
|`SUBSTR(?s,i,len)`|Substring (1-indexed)|`SUBSTR("hello",2,3)` → `"ell"`|
|`UCASE(?s)`|Uppercase|`UCASE("Hello")` → `"HELLO"`|
|`LCASE(?s)`|Lowercase|`LCASE("Hello")` → `"hello"`|
|`CONTAINS(?s,?sub)`|Contains|`CONTAINS("hello","ell")` → `true`|
|`STRSTARTS(?s,?pre)`|Starts with|`STRSTARTS("hello","he")` → `true`|
|`STRENDS(?s,?suf)`|Ends with|`STRENDS("hello","lo")` → `true`|
|`CONCAT(?a,?b,...)`|Concatenate|`CONCAT("a"," ","b")` → `"a b"`|
|`REPLACE(?s,?old,?new)`|Replace|`REPLACE("aab","a","z")` → `"zzb"`|
|`REGEX(?s,?pat)`|Regex match|`REGEX("hello","^h")` → `true`|

### Numeric Functions

|Function|Description|
|---|---|
|`ABS(?n)`|Absolute value|
|`ROUND(?n)`|Round to nearest integer|
|`CEIL(?n)`|Round up|
|`FLOOR(?n)`|Round down|
|`RAND()`|Random number 0-1|

### Date/Time Functions

|Function|Description|
|---|---|
|`NOW()`|Current datetime|
|`YEAR(?dt)`|Year component|
|`MONTH(?dt)`|Month component|
|`DAY(?dt)`|Day component|
|`HOURS(?dt)`|Hours component|
|`MINUTES(?dt)`|Minutes component|
|`SECONDS(?dt)`|Seconds component|

### Term Functions

|Function|Description|
|---|---|
|`BOUND(?var)`|Is variable bound?|
|`isIRI(?x)`|Is IRI?|
|`isBLANK(?x)`|Is blank node?|
|`isLITERAL(?x)`|Is literal?|
|`LANG(?lit)`|Language tag|
|`DATATYPE(?lit)`|Datatype IRI|
|`IRI(?s)`|Construct IRI from string|
|`BNODE()`|Create blank node|

### Aggregate Functions

|Function|Description|
|---|---|
|`COUNT(?x)`|Count values|
|`SUM(?x)`|Sum|
|`AVG(?x)`|Average|
|`MIN(?x)`|Minimum|
|`MAX(?x)`|Maximum|
|`GROUP_CONCAT(?x; SEPARATOR=",")`|Concatenate group|
|`SAMPLE(?x)`|Arbitrary value|

---

## Common Mistakes

### Wrong: Using != instead of NOT EXISTS for negation

```sparql
# WRONG - only filters existing matches, doesn't find missing
WHERE {
  ?p rdf:type i:Course .
  ?e i:course ?p ; i:day ?d .
  FILTER ( ?d != "MON" )           # misses courses with no enrollment!
}

# CORRECT
WHERE {
  ?p rdf:type i:Course .
  FILTER NOT EXISTS { ?e i:course ?p ; i:day "MON" }
}
```

### Wrong: Nested vs Independent OPTIONAL

```sparql
# WRONG - web only checked if email exists
OPTIONAL {
  ?s i:email ?e
  OPTIONAL { ?s i:web ?w }
}

# CORRECT - independent optionals
OPTIONAL { ?s i:email ?e }
OPTIONAL { ?s i:web ?w }
```

### Wrong: Combined properties in OPTIONAL

```sparql
# WRONG - both must match or neither
OPTIONAL { ?s i:email ?e ; i:web ?w }

# CORRECT - independent optionals
OPTIONAL { ?s i:email ?e }
OPTIONAL { ?s i:web ?w }
```

### Wrong: Missing DISTINCT with UNION/patterns that multiply results

```sparql
# May return duplicates
SELECT ?p ?c
WHERE {
  { ?e i:course ?p ; i:day "MON" }
  UNION
  { ?e i:course ?p ; i:day "FRI" }
}

# CORRECT
SELECT DISTINCT ?p ?c
WHERE { ... }
```

---

## Exam-Style Complete Query

```sparql
# Average study results for students with at most 10 courses
# Ignore enrollments with undefined results
# Describe students by full names

PREFIX i: <http://nosql.opendata.cz/school/terms#>
PREFIX s: <http://nosql.opendata.cz/school/>

SELECT ?f ?l (AVG(?r) AS ?avg)
FROM <http://nosql.opendata.cz/school/pdb-251/>
WHERE {
  ?s rdf:type i:Student ;
     i:name [ i:first ?f ; i:last ?l ] ;
     i:enroll [ i:course ?c ; 
                i:result ?r ; 
                i:semester s:semester251 ] .
}
GROUP BY ?s ?f ?l
HAVING (COUNT(?c) <= 10)
```