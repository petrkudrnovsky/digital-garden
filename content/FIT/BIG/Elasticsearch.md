= distribuovaný full-text vyhledávač a databáze v jednom
- databáze je to, protože si oindexovaná data ukládá do vlastní struktury dat
- komplexní indexování (uložená data komplexně indexuje, aby k nim mohl rychle přistupovat)

Obsahuje a podporuje různé textové analyzéry (včetně toho českého - rozumí skloňování) out of the box

Komunikuje pomocí [[REST protokol]]ful API a přes [[HTTP protokol]]
## Principy distribuované indexace
##### Index
- je to taková kolekce souvisejících dokumentů -  "tabulka jednotlivých dokumentů"
	- index obsahuje dokumenty a jejich fieldy
- používá invertovaný index
- obsahuje **mapování** = popisuje vnitřní strukturu vkládaných dokumentů
	- to většinou nejde moc upravovat (byla by nutná kompletní reindexace)
##### Clustery a uzly (architektura)
- cluster obsahuje více uzlů, které spolu spolupracují
- uzly jsou:
	- master node - spravuje metadata clusteru a konfiguraci ostatních nodů
	- data node - ukládá data a provádí operace nad nimi
	- ingest node - provádí předzpracování dat před indexací
	- client node - jako prostředník mezi data nodem a master nodem
##### Distribuce dat
- pomocí **konzistentního hashování** - určení shardu, který bude obsahovat konkrétní dokument
##### Replikace a vysoká dostupnost
- každý index je rozdělen na menší části zvané shardy
	- data jsou pak paralelně zpracovávána a ukládána na více uzlech
		- jeden shard může být replikovaný na více uzlech
	- máme primární shardy a pak repliky shardů (což jsou přesné kopie a v případě výpadku primárního se mohou stát primárním)
		- systém je tedy dostupný i při selhání části infrastruktury
##### Proces indexace
= proces přidání dokumentu mezi data (invertovaného) indexu
- nejdřív se data musí přijmout (ve formátu JSON, [[Logstash]] může transformovat data i z jiných formátů)
- nejdřív musí proběhnout analýza (neboli pre-processing)
	1. aplikují se filtry (text se upraví do požadované podoby)
	2. data se vloží do tokenizéru - rozdělí text na jednotlivé tokeny
	3. filtrování tokenů - např. odstranění nežádoucích tokenů (předložky, spojky - stop slova)
- po zpracování je dokument přiřazen konkrétnímu shardu pomocí hashovací funkce
- dokument se nejdřív uloží do primárního shardu a pak se replikuje do replik
	- dokument se zapíše do in-memory bufferu a do transakčního logu
##### Výhody distribuované indexace
- dobře se horizontálně škáluje (dobře se přidávají další uzly -> větší kapacita, lepší paralelní zpracování)
- vysoká dostupnost - díky replikaci je to odolné vůči výpadku
- paralelní zpracování - díky rozdělení na shardy (tedy každý shard má část dat)
- efektivita - díky konzistentnímu hashování se data a dotazy efektivně rozdělují mezi jednotlivé shardy
## Near Real-Time Search
- vyhledávání v téměř reálném čase
- tedy mezi změnou dat a jejich dostupností existuje malinká prodleva
- je to trade-off mezi výkonem, konzistencí a rychlostí
- *tedy vložené/aktualizované dokumenty jsou dostupné většinou do 1 sekundy od změny*
- princip:
	- dokument se vloží nebo aktualizuje a data se zapíšou do
		- transakčního logu (commit log) - pro případnou obnovu dat
		- paměti (in-memory buffer) - dočasné uložení dat
	- Elasticsearch pravidelně dělá tzv. **refresh** 
		- proces, který data z paměti (in-memory buffer) převede do segmentů - tím se zpřístupní pro vyhledávání
		- defaultně je to každou 1 sekundu
	- jakmile jsou segmenty vytvořeny, jsou součastí indexu a může se vyhledávat
## CAP teorém
- upředňosťuje dostupnost a toleranci vůči rozdělení sítě (AP)
- konzistenci si může uživatel nakonfigurovat (jsou různé úrovně konzistence)