- nejpopulárnější [[Grafové databáze|grafová databáze]]
- open-source, implementovaný v Javě
### Multilabeled Property Multigraph
- tedy každý ==vrchol== a hrana mohou mít různé atributy
	- štítky (labels) určují datový typ
	- ==hrany==
		- hrany mají vždy směr a ten musí být specifikován při vložení hrany (směr je pak neměnný, immutable)
		- hrana má pouze jeden typ, který se pak nedá změnit
		- graf ale mohu procházet oběma směry (bez vlivu na výkon)
			- tím pádem mohu ignorovat směrovost jednotlivých hran
	- ==vlastnosti== - reprezentují metadata (klíč-hodnota)
		- jsou uložené pomocí "property map" (je flat and homogenous) lokálně u každého uzlu
		- tímto jsem schopen ke každému node uložit doplňující data
	- ==cesta== (path) - a sequence of interleaved (vložených) nodes and relationships
- *každá hrana může mít jiná metadata a jiné popisy a díky tomu mohu definovat komplexní vztahy mezi objekty a každý vztah si mohu zvlášť nadefinovat*
### Datové typy
- každá vlastnost (property) má typ (string, boolean, integer, float, list)
	- datové typy se nemusí explicitně specifikovat, Neo4j si je dynamicky otypuje sama
- listy (seznamy) musí být homogenní (obsahovat pouze jeden typ a nesmí obsahovat `null`)
- složitější datový typy se spíš modelují pomocí grafu a vztahů mezi nody
- pro konkrétní node/edge jsou data uložena v tzv. property map:
	- jsou povoleny jenom atomické hodnoty a homogenní arraye
### Architektura
- může být ve formě client-server nebo tzv. **embedded database**
	- Embedded database
		- databáze, která je přímo propojená/integrovaná s aplikací
		- aplikace se nikam nepřipojuje, databáze je na stejném node jako aplikace a komunikace s databází je přímo v aplikaci
- rozšíření
	- Neo4j Browser - rozhraní pro dotazování a vizualizace, správce dat
	- Neo4j Desktop - nástroj pro správu a vývoj databáze
	- aktivní komunita neustále přidává další pluginy a rozšíření + Neo4j podporuje integraci s hlavními programovacími jazyky (přes Neo4j drivers)
- plně podporuje transakce a [[ACID]]
	- *atomicity, consistency, isolation, durability*
	- konzistence a integrita dat v grafu je tím zachována
	- díky transakci mohu změnit více dat na grafových datech najednou a přitom zajistit, že jsou data vždy v konzistentním stavu
- podporuje horizontální škálování a umožňuje rozdělení dat na více serverů (+ paralelní zpracování)
	- ale nedají se horizontálně škálovat tak dobře jako jiné [[NoSQL databáze]]
	- škálování grafových dat je obtížnější
### Nevýhody
- shardování grafových dat je velmi složité, tak je horizontální škálování omezenější než např. pro [[Apache Cassandra]], jde to, ale není to příliš doporučené
- dále neumí dobře agregovat data
- nemá tak vyvinuté transakce než RDBMS
### Vlastní dotazovací jazyk Cypher
- deklarativní jazyk speciálně vytvořený pro práci s grafovými daty
	- podobně jako [[SPARQL]] funguje na subgraph matchingu
- sekundární jazyk se jmenuje Gremlin - umožňuje procházení grafu (imperativní)
- používá ASCII art pro dotazování (šipečky, vizuální znázornění vrcholů a hran)
	- `()` pro vrchol
	- `-->, --, <--` pro vztahy
- podobný jazyku [[SQL]] syntaxem a pak [[SPARQL]] svým vyhodnocováním
	- ale je tady velký rozdíl, že v rámci Cypheru lze jednotlivé příkazy lze několikrát opakovat v jednom dotazu
	- je to sekvence jednotlivých příkazů a výstup z prvního jde jako vstup do dalšího
	- samotný výstup je pak tzv. solution sequence
- příkazem `MATCH` se specifikuje grafový vzor, který má databáze hledat
```cypher
MATCH (a:AIRPORT)-[:DIRECT]->(b:AIRPORT)
RETURN a, b;

(a:AIRPORT) - vrchol a s labelem AIRPORT
(a:AIRPORT) -[DIRECT]-> (b:AIRPORT)

Pro vlastnosti (properties) se používá JSON syntax:
MATCH (s{code:'sf'})-[:DIRECT]->(d) 
RETURN s,d;

Vytvoření záznamu
CREATE (a:PERSON {name: 'Petr'})-[:KNOWS]->(b:PERSON {name: 'Jana'});

Smazání celé databáze
MATCH (n)
DETACH DELETE n;
```
- pomocí příkazů `CREATE, DELETE, SET, REMOVE` se dá manipulovat s vrcholy, hranami, labels a i s properties
- node patterns:
	- pro vybrání konkrétní node: `(m)`
	- label condition: `(m:MOVIE)`
		- vybrané nody musí být tento label (může jich být i více - všechny musí být splněny)
	- property map condition: `(m:MOVIE { title: "Medvídek", year: 2007 })`
		- všechny properties musí být splněny
		- na pořadí nezáleží
- relationship patterns
	- specifikuje se směr `-->, <--`, pokud se použije `--`, na směru pak nezáleží
	- obecný zápis: `()-[r]->()`
	- type condition: `()-[r:PLAY]->()`
		- může být i více typů, matchnutý musí být minimálně jeden z nich
	- property map condition: `()-[r:PLAY {role: "Jakub" }]()`
		- všechny conditions musí být splněny
	- variable length mode - je možné nastavit podmínky pro délku nalezené cesty (by default se hledá cesta o délce 1 s danými podmínkami)
		- `()-[r:FRIEND *..2]-()` (zde se může najít max. délka 2, všechny edges "po cestě" musí splňovat specifikované podmínky)
	- každý relationship se může matchnout jenom jednou
		- `(a)-[:FRIEND]-()-[:FRIEND]-(b)`
			- vezmi všechny vrcholy a, které jsou kamarádi s nějakým vrcholem, který je kamarád s dalším vrcholem
			- a i b mohou být ten stejný node, což je v pořádku
				- ale musí tam existovat dvě různé edges s FRIEND vlastností
				- každá edge se totiž matchne jenom jednou - to efektivně zamezí nekonečnému zacyklení
		- `(a)-[:FRIEND]-()-[:FRIEND]-(a)`
			- a vrcholy mohou používat víckrát (pokud je označím stejnou variable)
		- tedy: vrcholy mohou být matchnuté víckrát v dotazu, relationships/edges nikoliv
- RETURN
	- vrací výsledky, musí být poslední v příkazu
	- modifikátory: DISTINCT, ORDER BY, SKIP, LIMIT
- neexistuje GROUP BY nebo HAVING
	- groupování probíhá automaticky, když se zavolá alespoň jedna agregační funkce
		- pak se sloupce ve výsledku rozdělí na dvě skupiny
			- agregační sloupce (všechny sloupce, kde je volána agregační funkce)
			- grouping columns
				- zbytek sloupců, stanou se z nich klasifikační sloupce
## Využití
- sociální sítě - vztahy mezi uživateli, analýza dosahu příspěvků
- doporučovací systémy - vztahy mezi uživateli a produkty
- optimalizace dopravy, geografická data
- rozsáhlé rodokmeny
- vyhledávání podvodů
- znalostní grafy pro AI aplikace
## CAP teorém
- splňuje CA (konzistence, dostupnost)
- nedistribuují data
## Možnosti nasazení
- lokální instalace
- samostatný server
- embedded verze (např. v Javové aplikaci)
- cloud verze 