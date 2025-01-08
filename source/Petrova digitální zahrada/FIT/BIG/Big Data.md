> Termín Big Data označuje manipulaci s datasety tak velkými, že je nemožné nebo velice obtížné s nimi manipulovat za pomocí tradičních nástrojů a databází (převážně relačních).
- ale ve skutečnosti moc žádná pevná definice neexistuje - spíš je to buzzword
- *prostě už není možné takové objemy mít na jednom počítači a nějak rozumně s těmi daty nakládat - proto vznikly frameworky, které umožňují efektivní práci s daty, které jsou rozprostřené po X počítačích najednou*
	- hlavně pak třeba nástroje jako [[Hadoop]] a [[Apache Spark]]

> NoSQL = Not only SQL
- nějaký NoSQL databáze vychází z SQL jazyka
# 5 V - charakteristika Big Data
- volume (objem) - obrovské množství dat (petabyty, exabyty)
- velocity - generování dat je velmi rychlé a stejně rychle se musí i zpracovávat
- variety (různorodost) - data nemusí být vždy konzistentní, čistá a strukturovaná
	- máme i nestrukturovaná a semi-strukturovaná data (XML, JSON)
	- jsou z různých zdrojů
- veracity (důvěryhodnost) - při vysokém množství dat může klesat jejich věrohodnost
	- důležitý je i původ a kvalita dat
- value - přidanou hodnotu datům dáme tak, že je analyzujeme, agregujeme, vizualizujeme
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
	- hlavně pro relační databáze
- horizontální škálování (*do šířky*)
	- přidávání dalších uzlů (další počítače, další servery), levnější
	- využití v cloudu, problémem je síť
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

