- aby search engine rozuměl obsahu na stránce
	- na úrovni nějakých faktů (=znalostí), ne na primitivech, jako klíčová slova
		- syntaktický web je založený na primitivech právě - dnešní web právě jede na tomto principu - hlavně se prezentuje pro člověka (který tomu rozumí), ale není to poskládané tak, aby z toho počítač pochopil hlubší propojení, context, návaznosti atd.

>*"The Semantic Web is an extension of the current web in which information is given well-defined meaning, better enabling computers and people to work in co-operation."*

- sémantický dotaz
	- umím pojmenovat objekty, jejich velikosti, rozsahy atd. (ptající se rozumí tomu tématu)
		- např. SQL má sémantické dotazy
		- teď na webu se vyhledává pomocí klíčových slov
	- dotaz např. "Najdi mi domy v New Yorku, které stojí tolik a tolik a nejsou ze dřeva" - tohle už umí jazykové modely (jako chatGPT), ale to jsou natrénované modely, ne vyhledávače
		- teď přichází MS s BingAI a už dochází k nějakému propojení jazykového modelu a vyhledáváče
- kvůli staré syntaktické struktuře webu se stále hledá podle klíčových slov, ale snažíme se to překlopit směrem k sémantickému vyhledávání
- chceme dosáhnout ==strojové čitelnosti dat== (opravdu porozumění daným informacím)
	- to teď umí už jazykové modely

### HTML
- strukturování obsahu fulltextu, slouží jen pro prezentování 
### XML 
- semi-strukturovaná data - struktury (uzly ve stromu)
	- nestruktury - vložený fulltext mezi tagy
- XSLT - data jsou oddělená v XML formátu a je možné je "mapovat" do PDFka, HTML atd.
	- první krok k sémantickému webu
- problém je, že to mohu napasovat na menší objemy dat, ne na celý web
	- abychom to mohli udělat, tak potřebuji něco, co mě odstíní od toho mít nějakou velkou strukturovanou databázi at

**Ontologie** (v Computer Science) je formální specifikace znalosti jako množina conceptů v určité doméně + vztahy mezi těmito koncepty (znalostmi)

### RDF = resource descriptor framework
- abstraktní datový model pro konceptuální modelování dat
	- jak grafových, tak stromových, relačních atd.
- lze serializovat např. do XML
- všechny entity jsou globálně identifikované
	- vytvářím jeden velký globální dat faktů - to přesně chci
- více vyhovuje ontologii - už mohu mít nějaké znalosti a pomocí RDFka mohu spcifikovat, jaké jsou mezi nimi vztahy
- RDFS = RDF Schema
	- specifikace vztahů a konkrétních typů
- díky RDFku mám základní infrastrukturu pro definování **faktů** 

### SPARQL
- dotazovací jazyk pro RDF
- 4 základní queries (SELECT, CONSTRUCT, ASK, DESCRIBE)

### OWL
- = Web Ontology Language
- jazyky, které reprezentují znalosti (concepty) - přidává jim popis, deskriptivní logiku
- spolupracuje s RDF - je to sémantické rozšíření RDF

### FOAF (friend of a friend)
- založeno na RDF a OWL
- ontologie popisu osob, aby tomu rozuměly stroje

### Problémy
- části sémantického webu jsou úspěšné, viz výše, ale celkově WWW není sémantický
	- autoři stránek by museli vedle svých stránek ještě nabízet informace ve formě RDF (upravit jejich stránky) a to se nikomu nechce, protože (zatím) to moc nepřináší výhody
**Background knowledge**
- náročné téma, napíšu třeba: "Chci sci-fi knihu, kde není vesmírná loď"
	- podle klíčových slov by tohle určitě nešlo - sci-fi tam pravděpodobně nebude, vesmírnou loď tam nechci atd.
		- je potřeba tento dotaz sémanticky rozebrat a rozumět tomu
Databáze produtků, velké balíky dat atd.
- nelze scrapnout celou databázi produktů Alzy atd., takže dotazy na konkrétní srovnání produktů jsou také náročné

Možné řešení
- lepší motivace - správně sémantické weby by mohly být upřednostňovány Googlem
- dobré nástroje pro rychlý a snadný převod existujících stránek do RDF, XML
- propojená data (linked data) - anotování dat a jednotlivých zdrojů na URIs - propojování s dalšími

### Sémantizace webu
- syntaktika webu už přestala být dostačující a tak se začalo sémantizovat
- základy technologií
	- Unicode - sjednocení abeced po celém světě
	- URI - identifikace nejakého zdroje (to nemusí být vůbec žádná stránka)
	- na těchto základech se může stavět dále