### Exam topics
- Linked Data: principles (identification, standard formats, interlinking, open license), Linked Open Data Cloud
- SPARQL: graph pattern matching (solution sequence, solution, variable binding, compatibility of solutions), graph patterns (basic, group, optional, alternative, graph, minus); prologue declarations (BASE, PREFIX clauses), SELECT queries (SELECT, FROM, and WHERE clauses), query dataset (default graph, named graphs), variable assignments (BIND), FILTER constraints (comparisons, logical connectives, accessors, tests, …), solution modifiers (DISTINCT, REDUCED; aggregation: GROUP BY, HAVING; sorting: ORDER BY, LIMIT, OFFSET), query forms (SELECT, ASK, DESCRIBE, CONSTRUCT)
# Linked Data
- related: [[Sémantický web]], [[RDF]]
- it's a method of publishing structured and interlinked data in a way, so machines can automatically process them
- principles:
	- identify resources using URIs, or even better using URLs
	- publish data about resources in standard formats using [[HTTP protokol|HTTP]]
	- mutually interlink resources to form the Web of Data
	- release the data under an open license
- Linked Open Data Cloud = a interconnected web of publicly available datasets published using Linked Data principles
# [[SPARQL]]
- [[SPARQL - cheatsheet]]
# Tutorial notes
- it's better to keep the same prefix names as in the source file
	- but I can change it, formally it does not matter
- `{}` mean one graph pattern
	- we can have more of them in one query
```ttl
s:teacher5
    i:teach [
        i:course s:course5 ;
        i:semester s:semester251 ;
        i:day "WED" ;
        i:time "15:40" ;
        i:place s:roomS3 ;
        i:length "90" ;
    ] .
```
- toto je zkrácený zápis blank node
	- dalo by se to taky zapsat jako s:teacher5 i:teach \_
		- a pak: \_ i:course s:course5 ;
		- atd. atd.