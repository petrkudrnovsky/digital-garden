## Overview

- **Type**: Key-Value Store (NoSQL)
- **Architecture**: Sharding + Peer-to-Peer Replication
- **CAP**: AP System (Availability + Partition Tolerance)
- **Implementation**: Erlang
- **Interface**: HTTP API, Protocol Buffers, Client Libraries

---

## Data Model

### Hierarchy

```
Instance → Bucket Types → Buckets → Objects (Key-Value Pairs)
```

### Object Structure

- **Key**: Unicode string (unique within bucket)
- **Value**: Any binary data (text, JSON, image, etc.)
- **Metadata**: Content-Type (MIME), Vector Clock, Timestamp

### Bucket Type

- Logical collection of buckets
- Shared configuration (replication factor, quora)
- Namespace for buckets

### Bucket

- Logical collection of key-value objects
- Can override bucket type properties

---

## HTTP API with cURL

### cURL Options

|Option|Description|
|---|---|
|`-X METHOD`|HTTP method (GET, PUT, POST, DELETE)|
|`-H 'header'`|Add HTTP header|
|`-d 'data'`|Request body data|
|`-i`|Include response headers|

---

## CRUD Operations

### URL Pattern

```
/types/{type}/buckets/{bucket}/keys/{key}
```

Or without bucket type (uses default):

```
/buckets/{bucket}/keys/{key}
```

### CREATE - Insert with Specified Key (PUT)

```bash
curl -i -X PUT \
  -H 'Content-Type: text/plain' \
  -d 'Ivan Trojan, 1964' \
  http://localhost:8098/buckets/actors/keys/trojan
```

### CREATE - Insert with Auto-Generated Key (POST)

```bash
curl -i -X POST \
  -H 'Content-Type: application/json' \
  -d '{"name": "Ivan Trojan", "year": 1964}' \
  http://localhost:8098/buckets/actors/keys
```

Returns key in `Location` header (e.g., `4zmJhCNhM4h6mUJVw35CkOuNZ28`)

### READ - Get Object (GET)

```bash
curl -i -X GET \
  http://localhost:8098/buckets/actors/keys/trojan
```

**Response Headers:**

```
Content-Type: text/plain
X-Riak-Vclock: a85hYGBgzGDKBVI8...
Last-Modified: Sun, 25 Sep 2022 15:14:05 GMT
```

### UPDATE - Replace Object (PUT)

```bash
curl -i -X PUT \
  -H 'Content-Type: text/plain' \
  -d 'Ivan Trojan, 1964, Czech Actor' \
  http://localhost:8098/buckets/actors/keys/trojan
```

⚠️ **Note**: Updates completely replace the object!

### DELETE - Remove Object (DELETE)

```bash
curl -i -X DELETE \
  http://localhost:8098/buckets/actors/keys/trojan
```

---

## Bucket Operations

### List All Buckets

```bash
curl -i -X GET http://localhost:8098/buckets?buckets=true
```

```json
{ "buckets": [ "actors", "movies" ] }
```

⚠️ **Warning**: Expensive operation - don't use in production!

### List All Keys in Bucket

```bash
curl -i -X GET http://localhost:8098/buckets/actors/keys?keys=true
```

```json
{ "keys": [ "trojan", "machacek", "schneiderova" ] }
```

⚠️ **Warning**: Expensive operation - don't use in production!

---

## Bucket Properties

### URL Pattern

```
/types/{type}/buckets/{bucket}/props
```

### Get Properties (GET)

```bash
curl -i -X GET http://localhost:8098/buckets/actors/props
```

### Update Properties (PUT)

```bash
curl -i -X PUT \
  -H 'Content-Type: application/json' \
  -d '{ "props": { "n_val": 3, "w": "all", "r": 1 } }' \
  http://localhost:8098/buckets/actors/props
```

### Reset All Properties (DELETE)

```bash
curl -i -X DELETE http://localhost:8098/buckets/actors/props
```

### Reset Specific Properties (DELETE)

```bash
curl -i -X DELETE \
  -H 'Content-Type: application/json' \
  -d '{ "props": { "search_index": "" } }' \
  http://localhost:8098/buckets/actors/props
```

### Important Properties

|Property|Description|Values|
|---|---|---|
|`n_val`|Replication factor|Integer (default: 3)|
|`r`|Read quorum|Number, `all`, `quorum`|
|`w`|Write quorum|Number, `all`, `quorum`|
|`search_index`|Associated search index|Index name|
|`datatype`|CRDT data type|`counter`, `set`, `map`|
|`allow_mult`|Allow sibling objects|`true`/`false`|

---

## Data Types (CRDTs)

### Overview

CRDTs = Convergent Replicated Data Types

- Automatic conflict resolution
- Require bucket type with `datatype` property set
- Require `allow_mult: true`

### URL Pattern for Data Types

```
/types/{type}/buckets/{bucket}/datatypes/{key}
```

### Counter

- Integer value (positive/negative)
- Operations: `increment`, `decrement`
- **Convergence**: All increments/decrements eventually applied

**Initialize/Update Counter:**

```bash
curl -i -X POST \
  -H 'Content-Type: application/json' \
  -d '{ "increment": 5 }' \
  http://localhost:8098/types/counters/buckets/movies/datatypes/views
```

**Decrement:**

```bash
curl -i -X POST \
  -H 'Content-Type: application/json' \
  -d '{ "decrement": 2 }' \
  http://localhost:8098/types/counters/buckets/movies/datatypes/views
```

**Get Counter:**

```bash
curl -i -X GET \
  http://localhost:8098/types/counters/buckets/movies/datatypes/views
```

```json
{ "type": "counter", "value": 4 }
```

### Set

- Unordered collection of unique binary values
- Operations: `add_all`, `remove_all`
- **Convergence**: Addition wins over removal

### Map

- Collection of name-value pairs
- Values can be: registers, flags, counters, sets, maps
- Field names must include type suffix: `field_register`, `field_flag`, etc.
- **Convergence**: Addition/update wins over removal

### Register

- Single binary value (e.g., string)
- **Convergence**: Most recent value wins
- ⚠️ Only inside maps!

### Flag

- Boolean: `enable` (true) / `disable` (false)
- **Convergence**: Enable wins over disable
- ⚠️ Only inside maps!

---

## Search 2.0 (Yokozuna)

### Overview

- Full-text search using Apache Solr
- Objects indexed automatically on change
- Process: `Riak Object → Extractor → Solr Document → Schema → Index`

### Extractors by Content Type

|Content-Type|Extractor|
|---|---|
|`text/plain`|Plain text|
|`text/xml`, `application/xml`|XML|
|`application/json`|JSON|
|`application/riak_counter`|Counter|
|`application/riak_set`|Set|
|`application/riak_map`|Map|

### Field Type Suffixes (for XML/JSON)

|Suffix|Type|Multi-value|
|---|---|---|
|`_s`|String|`_ss`|
|`_i`|Integer|`_is`|
|`_f`|Float|`_fs`|
|`_b`|Boolean|`_bs`|

### JSON Extraction Example

**Input:**

```json
{
  "title_s": "Samotari",
  "year_i": 2000,
  "details": { "rating_s": "***" },
  "genre_ss": [ "comedy", "drama" ]
}
```

**Extracted Fields:**

- `title_s` → "Samotari"
- `year_i` → "2000"
- `details.rating_s` → "***"
- `genre_ss` → \["comedy", "drama"\]

### XML Extraction Example

**Input:**

```xml
<movie year_i="2014">
  <title_s>Dira u Hanusovic</title_s>
  <genre_ss>comedy</genre_ss>
  <genre_ss>drama</genre_ss>
</movie>
```

**Extracted Fields:**

- `movie@year_i` → "2014"
- `movie.title_s` → "Dira u Hanusovic"
- `movie.genre_ss` → \["comedy", "drama"\]

---

## Search Setup

### Step 1: Create Index

```bash
curl -i -X PUT \
  -H 'Content-Type: application/json' \
  -d '{ "schema": "_yz_default" }' \
  http://localhost:8098/search/index/imovies
```

Or with default schema:

```bash
curl -i -X PUT http://localhost:8098/search/index/imovies
```

### Step 2: Associate Index with Bucket

```bash
curl -i -X PUT \
  -H 'Content-Type: application/json' \
  -d '{ "props": { "search_index": "imovies" } }' \
  http://localhost:8098/buckets/movies/props
```

⚠️ **Note**: Existing objects won't be indexed automatically!

---

## Search Queries

### URL Pattern

```
/search/query/{index}?wt={format}&q={query}&sort={criteria}&start={n}&rows={n}
```

### Parameters

|Parameter|Description|Example|
|---|---|---|
|`q`|Search query|`title_s:Samotari`|
|`wt`|Response format|`json`, `xml`, `csv`|
|`sort`|Sort order|`year_i desc,title_s asc`|
|`start`|Pagination offset|`0`|
|`rows`|Results per page|`10`|

### Search Conditions

**Term Search:**

```
title_s:Samotari
```

**Phrase Search (multiple words):**

```
title_s:"Dira u Hanusovic"
```

**Wildcard Search:**

```
title_s:*Bobule       (ends with Bobule)
title_s:Bob?le        (? = single char)
```

**Range Search:**

```
year_i:[2015 TO *}    (>= 2015, exclusive upper)
year_i:[2010 TO 2020] (inclusive both)
rating_i:{* TO 50]    (exclusive lower, <= 50)
```

**Logical Operators:**

```
genre_ss:action OR genre_ss:fantasy
genre_ss:action AND year_i:[2020 TO *}
NOT genre_ss:horror
(genre_ss:action OR genre_ss:drama) AND year_i:2020
```

### Search Example (with URL encoding)

```bash
curl -i -X GET \
  'http://localhost:8098/search/query/imovies?wt=json&q=year_i%3A2020'
```

### URL Encoding Reference

|Character|Encoded|
|---|---|
|space|`%20`|
|`:`|`%3A`|
|`*`|`%2A`|
|`?`|`%3F`|
|`[`|`%5B`|
|`]`|`%5D`|
|`{`|`%7B`|
|`}`|`%7D`|
|`(`|`%28`|
|`)`|`%29`|
|`"`|`%22`|
|`\`|`%5C`|

---

## Architecture Internals

### Riak Ring

- **Consistent Hashing**: hash(bucket, key) → 160-bit integer
- Ring divided into equal partitions
- Each vnode (virtual node) manages one partition
- Physical nodes interleaved for balanced distribution

### Replica Placement

1. First replica: determined by hash function
2. Additional replicas: consecutive partitions clockwise
3. **Hinted Handoff**: neighboring nodes cover for failed nodes

### Request Flow

1. Request arrives at any node (coordinating node)
2. Hash calculated → replica locations determined
3. Internal requests sent to all replica nodes
4. Wait for quorum responses
5. Return result to client

### Quorum Settings for Strong Consistency

```
w > n_val/2           (write quorum)
r > n_val - w         (read quorum)
```

Example with `n_val=3`: `w=2, r=2` ensures consistency

---

## Quick Reference

### Common Content Types

|Type|MIME|
|---|---|
|Plain Text|`text/plain`|
|JSON|`application/json`|
|XML|`application/xml`|
|HTML|`text/html`|
|JPEG|`image/jpeg`|

### HTTP Status Codes

|Code|Meaning|
|---|---|
|200|OK (GET, PUT success)|
|201|Created (POST success)|
|204|No Content (DELETE success)|
|300|Multiple Choices (siblings)|
|404|Not Found|

### Best Practices

1. **Don't** list all buckets/keys in production
2. **Do** use meaningful, structured keys
3. **Do** set appropriate Content-Type headers
4. **Do** use data types for automatic conflict resolution
5. **Do** configure quorum based on consistency needs