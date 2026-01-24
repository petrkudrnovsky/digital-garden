### Exam topics
- Scaling: scalability definition; vertical scaling (scaling up/down), pros and cons (performance limits, higher costs, vendor lock-in problem, …); horizontal scaling (scaling out/in), pros and cons, network fallacies (reliability, latency, bandwidth, security, …), cluster architecture; design questions (scalability, availability, consistency, latency, durability, resilience)
- Distribution models: sharding: idea, motivation, objectives (balanced distribution, workload, …), strategies (mapping structures, general rules), difficulties (evaluation of requests, changing cluster structure, obsolete or incomplete knowledge, network partitioning, …); replication: idea, motivation, objectives, replication factor, architectures (master-slave and peer-to-peer), internal details (handling of read and write requests, consistency issues, failure recovery), replica placement strategies; mutual combinations of sharding and replication
- CAP theorem: CAP guarantees (consistency, availability, partition tolerance), CAP theorem, consequences (CA, CP and AP systems), CA spectrum, ACID properties (atomicity, consistency, isolation, durability), BASE properties (basically available, soft state, eventual consistency)
- Consistency: strong vs. eventual consistency; write consistency (write-write conflict, context, pessimistic and optimistic strategies), read consistency (read-write conflict, context, inconsistency window, session consistency), read and write quora (formulae, motivation, workload balancing)
### Scalability
- = ability of a system to handle growing amount of data and/or queries without losing it's performance
- [[Big Data#Škálovatelnost]]
##### Vertical scaling
- in favor of strong consistency, easy implementation and deploying, no issues with data distribution (everything is on one node)
- drawbacks
	- even the most powerful machines have limits
	- the costs grow exponentially (especially in comparison to sum of computers with commodity hardware)
		- and upfront budget is often needed (buying an expensive server for a new project, so it can keep up in the future, when the project scales)
	- vendor lock-in (there are only a few manufacturers of large machines)
		- also human resources (specialized people for the specialized high-volume servers)
		- cannot migrate to different vendor
	- downtime when scaling up
##### Horizontal scaling
- adding more nodes into the cluster, often in NoSQL systems
- benefits:
	- commodity, cheap hardware
	- flexible deployment and maintenance
	- no SPoF
- drawbacks
	- more complexity (more management, programming model, handling more machines etc.)
	- different problems to solve:
		- data distribution, synchronization, data consistency, recovery...
	- false assumptions:
		- reliable network, topology does not change, only one administrator, secure network, infinite bandwidth, transport cost is zero, latency is zero...
### Cluster
- = a collection of mutually interconnected commodity nodes
- based on a share-nothing architecture
	- no sharing of physical components and operation systems
	- nodes communicate using messages
- in cluster, data are **distributed**:
	- sharding = placing different data on different nodes (to increase the total volume of data)
		- data that are accessed together should be stored together
			- we should avoid querying data across multiple shards
		- achieves uniform data distribution on nodes and balanced workload
		- we sometimes want to have the data in specific geographic locations
		- sharding strategies:
			- based on mapping structures:
				- data are randomly placed in shards (e.g. round-robin)
				- some central knowledge/index about the mapping of data/aggregates to shards has to be included (it has disadvantages)
			- based on general rules:
				- each shard is responsible for storing certain data
				- hash partitioning, range partitioning
		- sharding challenges:
			- on write: determine on which shard it should be stored
			- on read: only from the query data, determine, on which shard is the data to read them efficiently
			- the structure of the cluster is changing 
				- new/deleted/failed nodes
				- nodes may have outdated/incomplete information
				- partitioned network - synchronization messages are not coming through
	- replication = the same data on different nodes (to increase fault tolerance and performance)
		- replication factor = number of copies
	- in NoSQL databases, sharding and replication are often distributed
	- [[Big Data#Distribuce]]
### Master-slave replication architecture
- reading is possible from both Master and Slaves
	- when Master fails, reads can still be handled
- writing is possible only to Master - and the changes then propagate to Slaves
	- so there are no conflicts
	- inconsistency window (= time between write on Master and propagation to Slave nodes)
		- in this (usually) very small window, 2 users can read different data
	- writing to different nodes simultaneously is okay
	- master can be the bottleneck (failures and performance)
		- when it fails, new master needs to be appointed 
			- manually (user-defined)
			- automatically (cluster-elected)
- combined with sharding:
	- the roles of an individual node (server) can overlap
		- the node could be a master for some data/shards and/or slave for another
### Peer-to-peer replication architecture
- all nodes offer reading and writing data
	- they communicate the write events to other nodes (consistency issues)
- write-write conflict (consistency issues)
- no SPoF, no bottleneck, better scaling
- combined with sharding 
	- anything could be anywhere, different shards could be on any node in the network (but some rules can still apply)
***
### [[CAP teorém]]
> It is not possible to have a distributed system that would guarantee consistency, availability, and partition tolerance at the same time. Only 2 of these 3 properties can be enforced.
### [[BASE model]]