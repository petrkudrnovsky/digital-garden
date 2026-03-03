> Termín Big Data označuje manipulaci s datasety tak velkými, že je nemožné nebo velice obtížné s nimi manipulovat za pomocí tradičních nástrojů a databází (převážně relačních).
- ale ve skutečnosti moc žádná pevná definice neexistuje - spíš je to buzzword
- *prostě už není možné takové objemy mít na jednom počítači a nějak rozumně s těmi daty nakládat - proto vznikly frameworky, které umožňují efektivní práci s daty, které jsou rozprostřené po X počítačích najednou*
	- hlavně pak třeba nástroje jako [[Hadoop]] a [[Apache Spark]]

> NoSQL = Not only SQL
- nějaký NoSQL databáze vychází z SQL jazyka či se jím inspirují
# 5 V - charakteristika Big Data
- volume (objem, scale) - obrovské množství dat (petabyty, exabyty)
- velocity (speed) - generování dat je velmi rychlé a stejně rychle se musí i zpracovávat
- variety (různorodost, complexity) - data nemusí být vždy konzistentní, čistá a strukturovaná
	- máme i nestrukturovaná a semi-strukturovaná data (XML, JSON)
	- jsou z různých zdrojů, různé typy a různé struktury
- veracity (důvěryhodnost, uncertainty) - při vysokém množství dat může klesat jejich věrohodnost
	- důležitý je i původ a kvalita dat
	- neboli "uncertainty" due to inconsistency, incompleteness, latency, ambiguities or approximations
- value - přidanou hodnotu datům dáme tak, že je analyzujeme, agregujeme, vizualizujeme
	- the business value has to be found/revealed in the data
- validity - the data must be valid and accurate with respect to their intended use
	- also includes data governance
- volatility - a period of time when the data is valid and should be maintained
	- after some time, the data can become invalid
- variability - the data sources can develop and the data can differ
- venue - different data from different sources (on "one venue")
- vocabulary - includes "how to describe" the data, data models, semantics
- vagueness - the meaning of some big data patterns could be confusing, vague
- there are also C-characteristics:
	- cardinality
	- continuity
	- complexity
# Data mají svůj životní cyklus
1) sběr - nejdřív data nasbíráme (monitoring, scraping) - např. Apache Kafka
2) organizace dat - definování struktury, jmenných konvencí
3) uložení dat - do filesystému, bucketů (např. AWS), databáze
	- nějaká data chci jenom zprocesovat a zahodit (např. u machine learningu)
4) prohledávání a dotazování - databázový stroj, [[Elasticsearch]]
5) analýza dat a vizualizace - analytické nástroje (např. Kibana v rámci [[Elasticstack]])
# Principy distribuovaných systémů
### Škálovatelnost
- jak jsme schopni navyšovat výkon a objem
- vertikální škálování (*do výšky*)
	- vylepšování HW komponent pro lepší výkon, drahé, HW limity
	- nevýhodou je také tzv. manufacturer vendor lock-in (jenom pár výrobců na světě dělá opravdu výkonné komponenty a nedá se jen tak přejít od jednoho ke druhému) + downtime, když se upgraduje
	- hlavně pro relační databáze (a pro [[Grafové databáze]])
- horizontální škálování (*do šířky*)
	- přidávání dalších uzlů (další počítače, další servery), levnější
	- využití v cloudu, problémem je síť a větší složitost systému jako celku
	- je to více flexibilní - jde jednoduše přiidávat/odebírat podle potřeby
### Distribuce
- většinou chci mít více než jeden uzel (počítač/server), abych mohl zvládat větší objemy
- **sharding** - rozdělování (distribuce) dat na jednotlivé uzly pomocí hashovací funkce, která data rozdělí rovnoměrně
	- data se rozdělují do shardů (podle sharding klíče) a na jednom uzlu může být více shardů
	- umožňuje lineární škálování - přidáním dalších uzlů přidáme další shardy a můžeme tedy rozdělit zátěž na více uzlů
	- uživatel pak přistupuje jenom k tomu serveru, kde se nachází data o která žádá
- důležité je navrhnovat systémy tak, aby nebylo potřeba moc posílat data přes síť, protože je to časově a finančně náročný
	- nechci posílat data k výpočtu, ale chci posílat výpočet k datům (a provádět ho lokálně tam, kde jsou data uložené)
	- někdy se přesunu dat po síti nevyhneme, ale chceme to limitovat
### Replikace
- slouží k **zálohování** dat, lepší dostupnosti a odolnosti systému
- master-slave replikace (asynchronní)
	- master zajišťuje zápis, slaves se z něho pak synchronizují
	- čtení může být z jakéhokoliv, který obsahuje příslušná data
	- v případě selhání mastera se může nějaký slave stát masterem
- peer-to-peer replikace (synchronní)
	- uzly jsou si rovné, výpadek jednoho z nich není problém
	- je pomalejší kvůli konzistenci, uzly se při zápisu domlouvají (buď všichni nebo nadpoloviční většina)
# [[CAP teorém]]
- požadavky, které by měl distribuovaný systém splňovat (definuje kompromis mezi jednotlivými požadavky)
- u relačních databází máme [[ACID]] požadavky
# [[BASE model]]
- alternativa k [[ACID]], uvádí požadavky na distribuované systémy
- je to praktická aplikace principů [[CAP teorém]] 
# [[MapReduce]]
- programovací paradigma, které určuje, jak pracovat s velkými objemy dat a jak efektivně rozprostřít náročné úlohy na více uzlů
# [[NoSQL databáze]]
- [[Redis]]
- [[MongoDB]]
- [[Apache Cassandra]]
### [[NoSQL vs. relační databáze]]

