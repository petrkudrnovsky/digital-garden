[[Projektové řízení]]
[[Metodiky vývoje SW]]
#bi-swi
***
### Definice projektu
- časově ohraničená činnost/úsilí
- směřuje k vytvoření unikátního produktu / služby
- pomocí projektu se snažím dosáhnout nějaké změny
- projekt není nějaká dlouhodobá kontinuální práce

### Jak zahájit projekt jako projektový manažer?
1) musím celý projekt naplánovat 
	- kolik bude potřeba času
	- jak velký bude tým, jaké jsou jeho možnosti
	- kolik to bude celé stát
	- musím znát detailně scope celého projektu -> potřebuji **analytika**
2) analytik
	- dodá detailní specifikaci zadání/požadavků
	- odpovídá na otázku "Co bude systém/aplikace dělat?"
		- dát tam funkčnosti, které jsou dostatečným přínosem pro zákazníka (vzhledem k nákladům)
	- k tomu musí poznat zákazníka a jeho business (činnost)
		- [[Modelování obchodních procesů|model obchodních procesů]]
		- [[Diagram aktivit - UML|diagram aktivit]]
		- vytvoří zadání, aby bylo splnitelné a zároveň co nejvíce vyhovovalo zákazníkovi
	- [[Analýza požadavků|definování a analýza požadavků]] a specifikování rozsahu projektu
		- [[Modelování případů užití (Use Cases)|model případů užití]]
		- obecné požadavky na aplikaci
		- návrhy obrazovek (wireframes)
	- popsání dat, se kterými zákazník pracuje
		- [[Doménový model|doménový model]]
- výsledkem je **analytická dokumentace**

### Mám všechno zanalyzované, mohu se pustit do návrhu
- samotný návrh provádí tzv. Solution Architect
- výsledkem je **návrhová dokumentace**
- možnost navrhnout pomocí [[UCD (User Centered Design)]]

1) vyberu implementační jazyk
	- mohu zvolit buď procedurální jazyk (C, skriptovací jazyky) - ty jsou dobré pro nízkoúrovňové programování, ale jsou naprd pro větší informační systémy
	- nebo klasicky OOP jazyk (Java, [[Dotnet|C#]], C++, [[Web Development#PHP|PHP]])
	- musím zvážit
		- dostupnost jazyka na trhu (jsem schopný obstarat lidi i když mi někdo vypadne?)
		- dostupnost potřebných frameworků (a např. jejich dokumentace, komunita)
		- pokud zaměření na Windows -> C#
		- pokud nízké nároky na webhosting -> PHP
2) zvolím a popíšu způsob ukládání dat
	- struktura databáze a/nebo využití rozhraní (API)/služeb jiné komponenty
	- [[Databázový model|databázový model]]
	- jak budu data ukládat?
		- relační databáze (MySQL, PostgreSQL, Oracle, MS SQL Server)
			- výhodou standard SQL, rozšířené
			- nevýhodou potřeba mapovat objekty do relací
		- objektové databáze (Gemstone, Versant)
			- nemusí se mapovat objekty, ale ještě nejsou tak rozšířené
		- soubory
		- toto definují obecné (=nefunkční požadavky z [[Analýza požadavků|analýzy požadavků]]), můj rozpočet, budoucí rozvoj atd.
3) volba a popis architektury aplikace
	- [[Návrh architektury aplikace]]
4) analýza a popis vazeb na okolní komponenty
	- způsob integrace mezi okolní komponenty
	- předávané informace do okolí
	- scénáře spolupráce
5) realizace [[Modelování případů užití (Use Cases)|případů užití]]

### Samotná implementace
- architektonické vzory
	- je potřeba správně přidělovat zodpovědnosti (= závaznost něco dělat nebo něco vědět)
	- návrhové vzory [[GRASP]]
- je také komunikovat s databází: [[Perzistence dat]]
- abych si mohl lépe představit, jak mezi sebou budou jednotlivé objekty spolupracovat, tak si udělám [[Sekvenční diagram|sekvenční diagram]]
- z aplikace je pak jedna či více komponent - což je fyzická část systému, která se dá samostatně nasadit
	- svým [[Rozhraní (Interface)|rozhraním (Interface)]] je oddělená od ostatních komponent
- při psaní kódu nechci znovu a znovu vynalézat kolo a proto používám návrhové vzory
	- [[GoF návrhové vzory]]
	- [Design patterns na Refactoring Guru](https://refactoring.guru/design-patterns)

- implementace spočívá s přepisování scénářů případů užití do kódu + dodržování navrhnuté architektury a struktury
	- ještě není rozhodnuté všechno (na začátku), ale nová rozhodnutí by měla být v souladu s celkovou koncepcí projektu
- [[Objektové paradigma]]
- [[Základní pravidla pro implementaci SW]]
- [[Refaktoring kódu]] - když potřebuji změnit vnitřní strukturu kódu bez změny jeho vnějšího chování

### Kontrola své práce
Samozřejme nechci odevzdat zákazníkovi nekvalitní projekt/kód.
[[Quality Assurance - zajištění kvality]]
- tam si stanovím cíle, jakých zavedení QA chci dosáhnout
	- pozor, nemohu si dát všechny cíle - splnění nějakých cílů může znamenat zhoršení v jiných cílech aplikace
- jedním ze způsobů, jak udržovat kvalitu aplikace je [[Testování aplikace|psaní testů]].

### Nasazení aplikace
- provádí se se specializovanými nástroji (Maven, Gradle, npm...)
- typicky:
	1) stažení zdrojového kódu a knihoven v požadovaných verzích
	2) vytvoření spustitelného systému
	3) spuštění testů
	4) reporting
	5) generování dokumentace
- nasadit můžeme manuálně (je potřeba to celé připravit, konfigurace prostředí administrátorem), nastavování atd. -> celkem háklivé na chybu
	- nebo automatické nasazování

### Podpora a údržba aplikace
- je to služba pro uživatele systému a umožňuje řešit a reportovat problémy, které se během používání objeví
- [[Podpora a údržba systému]]

### Integrace
- samozřejmě, že zákazník chce, aby mohl zapojit novou aplikaci mezi ostatní v jeho organizaci - aby si mohly vyměňovat data a funkce
- [[Integrace aplikace]]




