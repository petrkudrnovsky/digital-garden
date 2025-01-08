- open-source distribuovaná sloupcová NoSQL databáze
- pro zpracování velkého množství nestrukturovaných (semi-strukturovaných) dat
- stojí na základě [[Hadoop#HDFS|HDFS]] a poskytuje sloupcově orientované ukládání dat (jako [[Apache Cassandra]]) 

- narozdíl od HDFS (který poskytuje sekvenční přístup k datům), HBase umožňuje **rychlý random access**
- HBase garantuje konzistentní zápisy na úrovni řádku díky podpoře [[ACID]] vlastností na úrovni řádku
### Srovnání s Apache Cassandra
- má architekturu master-slave
	- master node, region servers
- zaměření na náhodný přístup k datům a dávkové úlohy
- replikace pouze na HDFS