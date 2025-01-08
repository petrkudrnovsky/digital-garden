Hadoop je open-source framework pro "distribuované, rychlé, levné, bezpečné ukládání a zpracování velkých objemů dat jakéhokoliv typu"
- jde zpracovávat strukturovaná i nestrukturovaná data
- běží na běžném "commodity" HW, čímž snižuje náklady (místo výkonného serveru spíš víc méně výkonných)
- automatické replikace zajišťují dostupnost dat při výpadku
### Komponenty
##### HDFS
= Hadoop Distributed File System
- je to distribuovaný souborový systém pro ukládání dat v clusterech
- založený na architektuře master-slave
	- NameNode (master): metadata o uložených souborech + koordinuje přístup k souborům a operace čtení a zápisu
		- je to SPoF - bez něj je cluster nepoužitelný, řeší se to tzv. SecondaryNode (ale ten není všude)
	- DataNodes - ukládají samotná data a posílají "heartbeat" zprávy NameNodu
- princip používání
	- každý soubor se rozdělí na bloky (o stejné velikosti), které se uloží na různé DataNodes
		- rozdělení a správu řeší NameNode
	- data se ještě replikují, aby byla zvýšena odolnost vůči chybám (to zajištuje DataNode)
- výhody
	- škálovatelnost, odolnost vůči chybám, jednoduché ukládání velkých souborů
- nevýhody
	- nemá nízkou latenci a je pomalý při častých aktualizacích dat (preferuje přístup write once, read many)
##### [[MapReduce]]
- je to jedna z aplikací, která může běžet na YARN
- hlavně transformuje a analyzuje data uložených na HDFS
- "specifický výpočetní model pro dávkové zpracování dat"
##### YARN
= Yet Another Resource Negotiator
- je to **resource manager** v Hadoopu
- odděluje správu zdrojů (paměť, CPU, disk...) od výpočetních frameworků (jako je MapReduce a Spark)
	- takže můžu efektivně a flexibilně spouštět různé služby a funkce (dynamické přidělování zdrojů jednotlivým aplikacím)
	- přiděluje jednotlivé zdroje jednotlivým aplikacím v rámci Hadoop
	- v rámci jednoho clusteru může běžet více různých aplikací najednou
- algoritmy přidělování zdrojů
	- capacity scheduler
	- fair scheduler
	- FIFO scheduler

Hlavní komponenty
- Container
	- reprezentuje zdroj (CPU, memory, disk...)
	- v rámci kontejneru jsou spouštěny jednotlivé úlohy
- ResourceManager (master)
	- řídí alokaci zdrojů, udržuje si globální informace a dostupných zdrojích v clusteru
	- komunikuje s NodeManagery
	- dostává žádosti od ApplicationMastera
- NodeManager (slave)
	- běží na každém slave uzlu a sleduje jeho stav + spouští jednotlivé kontejnery
	- reportuje pro ResourceManager
- ApplicationMaster
	- každá aplikace ho má
	- žádá zdroje od ResourceManagera pro svoji aplikaci
	- řídí tasky a úlohy v rámci své aplikace
![[Pasted image 20241210150454.png]]
### Vhodné úlohy
- dávkové zpracování dat, zpracování logů, indexace
### Nevhodné úlohy
- ty vyžadující nízkou latenci či časté iterace (např. strojové učení)