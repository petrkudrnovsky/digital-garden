- je důležité mít data uložená mimo aplikaci v daném formátu (data by neměla být vázaná na konkrétní aplikaci)
	- protože kdyby to tak nebylo, tak to pak brání vývoji nových aplikací nad stejnými daty či komunikaci s ostatními aplikacemi (např. v podnikové síti)
	- když jsou nezávislé, tak mohu nad stejnou databází dat měnit/připojovat aplikace jak potřebuji
- datový formát je:
	- otevřený - může ho používat každý bez omezení 
	- uzavřený - např. staré MS Word formáty (které šly otevřít pouze v programu Word)
		- je potřeba licence, registrace, certifikace, speciální aplikace atd.
- data vnímáme na 3 úrovních
	- konceptuální - [[UML]] diagramy
		- nezáleží na technologii a reprezentaci
	- logická
		- jak jsou data strukturovaná a jaké nástroje používáme
	- fyzická
		- jak jsou data uložená fyzicky v úložišti
##### Datové modely
- sady nástrojů pro různou reprezentaci dat
- grafy - [[RDF]], LPG (Labeled Property Graph - např. reprezentace dat v [[Neo4j]])
- hiererchie/stromy - [[Javascript - DOM|DOM]], JSON 
- tabulky - relační modely
##### Datové formáty
- jak jsou data v rámci daného [[#Datové modely|datového modelu]] serializovaná v souborech, jak jsou reprezentovaná
- např. DOM je reprezentovaný v [[XML]] či [[HTML]]
	- relační data v CSV či SQL dump
	- grafová [[RDF]] data jsou v:
		- textové formě: N-Triples, N-Quads, Turtle, TriG, RDF/XML, JSON-LD, RDFa
		- binární formě: HDT
##### Datová schémata
- anotace a omezení pro konkrétní data v rámci [[#Datové formáty|datových formátů]], aby se dala data dobře popsat a validovat
- JSON - JSON Schema
- RDF - SHACL, ShEx (nemusím znát)

Identifikátory: [[URL, URI, URN, IRI]]
### Rozdíl mezi binárním a textovým formátem souboru
- binární
	- není čitelný textovým editorem
	- je čitelný hex editorem
	- struktura souboru je definována na bitové úrovni
- textový
	- obsahuje text
	- zobrazitelný textovými i hex editory
	- pomocí kódování (*character encoding*) jsou jednotlivé znaky zakódované do bytů
		- tedy oba formáty jsou ve formě bytů
	- různé konce řádků pro různé OS (Linux:  \\n, Windows: \\r\\n (*carriage return*))
	- BOM (= byte order mark) - magické číslo na začátku souboru, které určovalo jak je kódovaný text v souboru
		- teď už se skoro nepoužívá
***
## [[RDF]]
## [[SPARQL]]
## [[XML]]
## [[JSON]]
***
# SZZ otázky
- [[Grafový datový model RDF a jeho serializace (N-Tripples, RDF-Turtle). Dotazování nad daty v RDF - jazyk SPARQL]]
- [[Hierarchické datové formáty - XML a JSON. Dobře formovaný (well-formed) a validní (valid) XML dokument. XSD a JSON Schema. Formát JSON-LD]]