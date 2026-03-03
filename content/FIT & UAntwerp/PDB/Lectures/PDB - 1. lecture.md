### Exam topics
Big Data and NoSQL terms, V characteristics (volume, variety, velocity, veracity, value, validity, volatility), current trends and challenges (Big Data, Big Users, processing paradigms, …), principles of relational databases (functional dependencies, normal forms, transactions, ACID properties); types of NoSQL systems (key-value, wide column, document, graph, …), their data models, features and use cases; common features of NoSQL systems (aggregates, schemalessness, scaling, flexibility, sharding, replication, automated maintenance, eventual consistency, …)
### Definition of Big Data
- there is no standard definition
>Big Data is high volume (scale), high velocity (speed), and/or high variety (complexity) information assets that require new forms of processing to enable enhanced decision making, insight discovery and process optimization.
- my view: data so big, it's impossible to process them on a single machine/server and that includes data that require different handling other than traditional relational SQL databases
	- relational SQL database is like a Swiss knife
	- NoSQL database serves a specific purpose (but is worse in other use-cases)
- [[Big Data#5 V - charakteristika Big Data]] 
	- the amount of "V-characteristics" can change
### Relational databases
- [[SQL|SQL = structured query language]]
- [[ACID]] properties
	- efficient parallel/concurrent execution
	- transactions: flat sequences of READ, WRITE, COMMIT, ABORT operations
- example: [[PostgreSQL]]
- database normalisation
	- why?
		- elimination of data redundancy (less inconsistencies and less storage space)
			- that prevents update, insertion, deletion anomalies
			- no orphaned or incomplete data
		- data are more consistent and accurate
		- better maintenance in the future
	- 1NF, 2NF, 3NF, BCNF (Boyce‐Codd normal form)
	- cons?
		- mainly increased query complexity and lower performance
			- the higher the normal form is, the more tables you usually need to store that data - the data is scattered into many tables that need to be joined when querying
	- for production purposes, "denormalization" is used - to balance the pros/cons of normalization
### Current trends in Big Data
- volume is bigger and bigger
- variety, we are moving from structured to semi-structured or unstructured data
- velocity - going from batch processing (all at once) to streaming data
- trends
	- social media, LLMs, more people connected to the internet, more sensors in smart devices/cars etc.
		- big users - people online, web companies, AI companies
		- exponential growth in data volumes
		- SaaS, IaaS, PaaS
	- everything in cloud services (a lot of data at one place)
	- processing paradigms are shifting to be analyzing data in real-time
	- strong consistency is no longer critical requirement
### [[NoSQL databáze|NoSQL databases]]
- [[NoSQL vs. relační databáze]]