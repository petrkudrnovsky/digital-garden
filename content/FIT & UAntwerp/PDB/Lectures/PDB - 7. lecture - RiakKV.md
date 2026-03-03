### Exam topics
- Data model (key/value pairs), key management (real-world identifiers, automatically generated, structured keys, prefixes), basic CRUD operations, use cases, representatives, extended functionality (MapReduce, TTL, links, structured store, …)
- Riak: data model (buckets, objects, metadata headers); HTTP interface, cURL tool (options); CRUD operations (POST, PUT, GET, and DELETE methods, structure of URLs, data, headers), buckets operations (buckets, keys, properties); data types (Convergent Replicated Data Types: register, flag, counter, set, map; conflict resolution policies; usage restrictions), Search 2.0 Yokozuna (architecture; indexation and query evaluation processes; specific extractors: text, XML, JSON; SOLR document: extracted and technical fields; indexing schema: tokens, triples; full-text index creation, association and usage; query patterns: wildcards, ranges, …); Riak Ring (physical vs. virtual nodes, consistent hashing, partitions, replica placement strategy, hinted handoff, handling of read and write requests)
### [[Key-Value database]]

[[MapReduce]] can be also implemented using RiakKV
- but it's user-unfriendly, because of the weird implementation language of RiakKV (Erlang)
# RiakKV
- short explaining video on [Riak](https://www.youtube.com/watch?v=5P6omeGwQ8c&t=30s)
- [[RiakKV- cheatsheet]]
- open-source key-value store
- automatic sharding, peer-to-peer replication
- developed in Erlang language (functional)
### Data Model
- cluster is split into buckets (just collections of key-value pairs)
	- each bucket has a type
		- when not defined, `default` is used
		- for shared configuration of buckets (replication factor, read/write quorum...), for user permissions, for namespace functionality
- object = one key-value pair
	- key is unicode string and is unique within a bucket
	- objects have metadata
		- values have MIME types
		- last modification time, vector clock etc. (internal metadata)
		- metadata are essentially headers (similar to HTTP headers)
- data modelling strategies:
	- multiple buckets (one bucket for each entity)
		- easier key management
	- single bucket (everything in one bucket, values still represent different entities)
		- keys are often composed or prefixed (to differentiate the entities)
### Usage
- CRUD operations - based on key look-up
- additional functionalities: links (to interconnect the objects), search 2.0 (full-text search of the values), [[MapReduce]]
### Interface
- HTTP API - stateless connection
	- we can use [[curl (Client URL)]] to access the database
	- keys could be HTTP URLs
		- key: http://example.com/buckets/actors/keys/trojan, value: entity containing data
- Protocol Buffers API - more complex, using established connection for a longer time (?)
- Erlang API
- Client libraries for different programming languages allow for:
	- defining the Riak structure using classes, properties etc.
		- and the source files for Riak will be automatically generated
	- it also takes care of the serialization and materialization
### Operations
- create and update (POST, PUT)
	- if key is present -> PUT, it inserts or updates the given object
	- if key is missing -> POST, key is generated randomly and returned via header
	- buckets are created automatically immediately when I try to insert some data to them - there is no way to create buckets explicitly
		- but I have to create bucket types explicitly
- read (GET)
- delete (DELETE)
	- when the specified object does not exist, it does not matter (no error)
- list all existing buckets and list all available keys in a bucket
	- very inefficient operations (do not use in production)
	- RiakKV is based on peer-to-peer architecture, so to perform these operations it needs to get information from all cluster nodes and match it somehow before returning it
- retrieval, update and reset of bucket properties
	- for a bucket, I can set:
		- write/read quorum
		- number of replicas (replication factor)
		- associated data type with the bucket (if any)
		- if the updates and deletes can be full or incremental
	- if I delete the properties, there will always be some default properties
### Data types
- the problem in RiakKV is that, it's AP ([[CAP teorém]]), so there could be inconsistencies between replicas of the given object
- the solution: CRDTs (= Convergent Replicated Data Types)
	- in general: those are generic concepts of specific data types, which are made for resolving issues with consistency (each type is for a different real-world use-case)
		- they have permitted values, permitted operations and a **convergence rule** (a mechanism for the conflict resolution)
	- in RiakKV
		- counters
			- when created, initialized to 0
			- then increments and decrements are allowed (only relative operations, setting the counter to some fixed value is not allowed)
			- convergence rule: all requested increments/decrements will eventually be processed
		- sets
			- an unordered collection of unique values (e.g. strings)
			- addition and removal of one or more objects (relative adding and removing the objects)
			- convergence rule: addition wins over removal
		- maps
			- an unordered collection of embedded name-value pairs
				- names are strings and act as keys
					- are prefixed based on the value type
				- values could be anything (even maps again -> this allows for more complex structures)
			- convergence rule = addition wins over removal
		- registers
			- can store any binary value (e.g. string)
			- convergence rule: chronologically recent values win
			- can be stored only in maps, not on the top level
		- flags
			- boolean values (enable/disable)
			- convergence rule: enable wins over disable
			- also could be stored within a map (not on the top level)
- the CRDT data types are stored independently from the key-value objects
	- regular objects are in regular bucket types
	- CRDTs are in special bucket types with a `datatype` property set (to the desired datatype)
		- so they cannot be mixed within one bucket type
### Search 2.0 (Yokozuna)
- full-text search engine for Riak (built on top of Apache Solr)
- it indexes data and then searches them effectively
- indexation
	- when an object is added/changed
	- Riak object is extracted to Solr document and then it's indexed in Solr index
		- there are predefined extractors, but we can create custom
		- the result of the extractor is a list of fields to be indexed
			- for string extractor, the whole string value is indexed
			- for XML or JSON extractors, the elements or attributes are indexed separately
	- Solr document contains extracted field + additional information (identification of the Riak object to reference it back)
	- Solr schema contains triples:
		- (token value, field name, document id)
		- one index is associated with one bucket
- querying
	- Riak search query is translated to Solr query and Solr response then included the list of documents with relevance scores and IDs to match them to associated source objects
	- options when searching:
		- search query by itself
		- response writer (in what format do you want results?): json, csv, xml, php, ...
		- sorting (default is `score desc`)
		- pagination
	- types of searches:
		- term searches, phrase searches (has to be exact match)
		- wildcard searches (`?` - one arbitrary character, `*` - zero or more arbitrary characters)
		- range searches - between some range (inclusive/exclusive bounds)
			- `*` represents `+/- Infinity`
		- logical expressions (`AND, OR, NOT`)
	- since we use HTTP for querying, the characters need to be encoded, so they are safe
		- deactivate Solr metacharacters by escaping
		- URL encoding (spaces, asterisks, brackets etc.)
		- shell metacharacters also need to be supressed
### Architecture
- peer-to-peer replication architecture with sharding
	- each physical nodes runs more virtual nodes
	- read/write requests could be served from any node
- RiakKV is AP ([[CAP teorém]])
	- strong consistency could be achieved by strict read/write quorums, but it is discouraged
##### Riak Ring
- a way, how to distribute data on all replicas
	- including consistent hashing function (input: bucket name + object key)
- the ring is split into equally-sized disjoint partitions
	- each virtual node is responsible for exactly one partition
- first replica is determined by the hashing function
	- remaining replicas are in a consecutive partitions in clock-wise direction
##### Failed nodes
- solved by "Hinted handoff"
	- failing nodes are temporarily skipped and neighbours temporarily take the responsibility
	- when resolved, replicas are handed off to proper locations
- has high availability
##### Request handling
- the request could be handled by any node, it becomes a "coordinating node" for this request
- it calculates the hash function, determines the correct node with correct data and his replicas
	- it forwards the request, gets response and returns response/failure to the user