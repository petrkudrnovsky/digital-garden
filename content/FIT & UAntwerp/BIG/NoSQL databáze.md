Definition:
>NoSQL databases = Next generation databases mostly adressing some of the points: being non‐relational, distributed, open‐source and horizontally scalable. The original intention has been modern web‐scale databases. Often more characteristics apply as: schema‐free, easy replication support, simple API, eventually consistent, a huge data amount, and more.
### Features
- goal of NoSQL databases is to respect the real-world nature of data
	- move from traditional relation databases to NoSQL
- **scaling-out** instead of scaling-up (this is the traditional approach, vertical scaling)
	- => distributed systems across many hosts
- sharding, replication ([[Big Data#Distribuce]] a [[Big Data#Replikace]])
- a lot of processes are automated, there is no need for highly skilled relational database administrators
	- automatic recovery, automatic distribution, tuning...
- [[BASE model]] (more relaxed consistency)
	- ACID represents traditional and strong consistency
- schemalessness
	- in relational databases the schema is stricly enforced
	- in NoSQL the schema is relaxed or completely missing
		- but there is always some implicit schema (we have to maintain some schema to match with the applications that are using the database)
		- design changes are easier
- they are often open-source (community and enterprise versions)
- the API is also really simple, often on the base of HTTP or similar (stateless interface)
### Challenges
- a lot of NoSQL databases are still pre-mature with key features missing
- they are open-source, no strong support
- sometimes difficult to install and maintain
- limited support for business intelligence tools for analytics and ad-hoc queries
- no enough big data experts on the market
### Typy NoSQL databází
[[Key-Value databases]]
- [[Redis]], Valkey, Scalaris, Riak KV
[[Dokumentově orientovaná databáze]] / document stores
- [[MongoDB]], CouchDB
[[Sloupcové databáze]] / wide-column stores
- [[Apache Cassandra]], [[Apache HBase]]
[[Grafové databáze]]
- [[Neo4j]]

Další druhy mohou být:
- Native XML databáze (XPath, XQuery, XSLT)
	- dokumenty jsou v kolekcích (podobně jako v [[Dokumentově orientovaná databáze]])
	- stromová struktura (XML), nested elements, attributes, text values
	- Sedna, Tamino, MarkLogic
	- [[FIT & UAntwerp/MDF/XML]]
- [[RDF]] databáze (případ grafových databází)
	- dotazovací jazyk [[SPARQL]]
	- triples (subject, predicate, object) can be viewed as graphs
	- Apache Jena, MarkLogic, rdf4j
### Aggregates
- aggregate je datová jednotka s komplexní strukturou, je to kolekce dat, které chceme z nějakého důvodu držet při sobě
- příklad: value v key-value stores, dokument v MongoDB, column family v sloupcových databázích
- většinou se databáze pak designují, aby splnily atomicity ve smyslu "one aggregate at a time"
- jsou dva typy NoSQL databáze:
	- aggregate-ignorant: relational, graphs
	- aggregate-oriented: key-value, document stores, wide column stores