Umožňuje efektivní práci s daty, které mají složité a komplexní vztahy.

Data se ukládají ve formě grafu, kde:
- uzly (nodes) - entity nebo objekty
- hrany (edges) - vztahy mezi objekty
- vlastnosti (properties) - detailnější informace (lze přiřadit uzlům i hranám)
### Výhody:
- grafové operace (hledání nejkratší cesty, kostra atd.)
	- shortest path
	- all paths
	- Dijkstra - nejlevnější cesta mezi 2 uzly
	- A\* - optimalizační úlohy
- dotazy na podgrafy, similarity-based queries
### Nevýhody:
- nevhodné pro velké batch operace
- nevhodné pro ukládání opravdu velkých objemů dat - protože grafová databáze se špatně distribuuje
### Datový model
- property graphs
	- mohou být directed i undirected
	- obsahují nodes (vertices) a relationships (edges) between those nodes
### Typy grafových databází
- non-transactional = málo velkých grafů
- transactional = hodně malých grafů
### Zástupci
- [[Neo4j]] - nejpopulárnější grafová databáze
- Titan
- multi-model databases:
	- OrientDB, Virtuoso, ArangoDB