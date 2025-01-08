- je open-source, původně od Facebooku, nyní vyvíjí Apache Software Foundation
## Architektura
- používá se **masterless ring architektura** (peer-to-peer) - tedy neexistuje žádný hlavní uzel, všechny uzly jsou si rovnocenné (narozdíl od **master-slave** přístupu, kde odolnost stojí a padá na master uzlu)
	- tato architektura je velmi odolná vůči výpadku uzlu (žádný SPoF = single point of failure)
	- data se rozprostřují po celém kruhu
		- a dál se jednotlivé kruhy mohou nacházet na fyzicky oddělených datacentrech nebo v cloudu
		- kolikrát jsou replikované = to mi říká *replikační faktor* 
	- jednoduše se spravuje a škáluje
##### Komunikace mezi uzly
- pomocí protokolu **Gossip** - pomocí něho mezi sebou uzly komunikují
	- jmenuje se gossip, protože kromě informací o sobě předávají informace o ostatních známých uzlech
	- předává informace o stavu clusteru, jednotlivých uzlů a umístění dat
	- **Seed uzly** - slouží pro inicializaci uzlů v clusteru
		- nový uzel získá první informace o topologii a dalších uzlech právě ze seed uzlu a pak už může normálně komunikovat (pomocí Gossip protokolu)
	- v clusteru je více seed uzlů pro zajištění dostupnosti a stability
- heartbeat signály - posílají se pravidelně a informují o "životě" uzlu
- **Phi Accrual Failure Detector** - varianta failure detektoru
	- monitoruje dostupnost uzlů, sleduje intervaly mezi heartbeat signály
	- hodnota *phi* udává pravděpodobnost, že je uzel mrtvý
##### Replikace
- replikují se na základě **replikačního faktoru**
- existuje komponenta "Partitioner", která daná data rozděluje podle dané replikační strategie
- 2 replikační strategie:
	- SimpleStrategy - spíš jenom na testovací účely - pro data v 1 datovém centru
		- určí se uzel pro první repliku a pak se replikuje na další uzly po směru
	- NetworkTopologyStrategy - jde nastavit různé replikační faktory pro různá datová centra
		- určí se první uzel replikace a pak nejbližší po směru v jiném racku
- data se verzují pomocí **časových razítek** 
##### Odolnost
- existují samoopravné mechanismy
- data z nedostupných uzlů se automaticky redistribuují
	- pokud se zjistí, že je nějaký uzel nedostupný (pomocí Phi Accrual Failure Detectoru) a Gossip protokolu -> tak se data rozdistribuují na další uzly, aby se splnila replikační strategie a počet replikací
##### Škálovatelnost
- založeno na horizontálním škálování - hashovacími funkcemi se rozdělují data v clusteru
	- uzly jsou organizovány do kruhů (masterless ring architektura) a každý uzel je zodpovědný za určitý hashovací rozsah
- tedy stačí přidat další uzel do clusteru místo nakupování výkonnějšího HW pro existující uzly
	- a Apache Cassandra automaticky začne rozdělovat data i na tento uzel (tedy všechny uzly budou zodpovědné za menší rozsah dat)
## Databázový model
- máme základní struktury:
	- Keyspace (= databáze v relačních)
		- nejširší objekt v databázi - definuje chování dat (replikační faktor, replikační strategie, typ zápisu dat)
		- udržuje pohromadě všechny Column Families
		- každý keyspace může mít jiný replikační faktor
	- Column family (tabulka v relačních)
		- definuje strukturu dat
		- pokud chci nějaká data číst společně, je vhodné na to vytvořit column family
	- Row - nemá pevnou strukturu, každý řádek může obsahovat jiný počet sloupců
	- Column
	- Super Column = tabulka v tabulce
		- objekt rozšiřujeme o další vnořený objekt (časté využití)
![[Pasted image 20241208190156.png]]
- primární klíč
	- složený z partition klíče a jednoho či více clustering klíčů
		- partition - definuje, na jakém uzlu se data uloží (na základě hashové hodnoty)
		- clustering - určuje pořadí dat na daném uzlu
##### CQL - Cassandra Query Language
- je to SQLko bez joinů
- je možné definovat svoje uživatelské datové typy
## Distribuce dat
- data se dělí pomocí Partition key (rozděluje se na základě hashování, aby byla data konzistentně rozdělená mezi uzly)
- replikační faktor 3 = data budou uložena na 3 uzlech
- lze nastavovat úroveň konzistence (kolik uzlů musí odpovědět na požadavek):
	- ONE: data jsou čtena/zapsána na jeden uzel
	- QUORUM: nadpoloviční většina replik musí odpovědět
	- ALL: všechny repliky musí odpovědět
- kvůli distribuci dat Cassandra nativně nepodporuje operace, které vyžadují iteraci přes všechny hodnoty v jednom sloupci - jednotlivé části dat jsou po různých částech systému (díky partition keys), takže proiterovat je všechny by bylo velmi výpočetně náročné
	- lepší jsou agregační funkce, které jsou dobře zacílené (na jeden partition)
## CAP teorém
Základní nastavení (AP):
![[Pasted image 20241208191630.png]]
- není splněna konzistence - data se nakonec zapíšou, ale neexistuje garance, kdy se tak stane
Je možné překonfigurovat do CP
