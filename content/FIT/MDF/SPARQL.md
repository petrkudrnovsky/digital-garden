- SPARQL je dotazovací jazyk pro [[RDF]] data
- open-source framework pro práci s RDF a SPARQL: [Apache Jena](https://jena.apache.org/index.html)
	- přímo SPARQL server je pak: [Apache Jena Fuseki](https://jena.apache.org/documentation/fuseki2/index.html) 
# SPARQL endpoint
- dotazy chodí na endpoint většinou přes [[HTTP protokol]]
- vstupem je SPARQL query
- výstupem jsou data ve formátu RDF, CSV, JSON, XML... (co potřebuji)
- známé příklady
	- [Wikidata Query Service](https://query.wikidata.org/)
		- komunitně budovaná databáze
	- [Národní katalog otevřených dat](https://data.gov.cz/sparql)
# Syntax
- má syntaxi velmi podobnou [[RDF#Turtle]] serializaci
- `?osoba ex:studujeObor ?obor`
	- "najdi mi všechny trojice, kde platí tento vzor"
	- v praxi najde všechny osoby studující nějaký obor
# Kroky vyhodnocení dotazu
1\. krok - pattern matching
- nalezne všechny možné trojice, které vyhovují patternu dotazu
- místa, kde je `?` se berou jako žolíky (matchuje se jakákoliv hodnota)
- pokud se nalezne shoda, tak se pokračuje na krok č. 2
2\. krok - variable binding
- výsledkem je tabulka řešení, která obsahuje jednotlivé možné kombinace proměnných, které hledáme
- vlastně se do hledané proměnné přiřadí aktuální hodnota a uloží se do tabulky
	- doplňují se chybějící informace za proměnné
# Příklad dotazu
```sparql
PREFIX ex: <http://example.com/namespace/>
SELECT ?osoba ?jmeno ?prijmeni
WHERE {
  ?osoba a ex:Student ;    # "a" je zkratka pro rdf:type
          ex:jmeno ?jmeno ;
          ex:prijmeni ?prijmeni .
  FILTER (lang(?jmeno) = "cs")   # příklad filtru podle jazykové značky
}
ORDER BY ?prijmeni
LIMIT 100
```
- `PREFIX` - je potřeba definovat všechny použité prefixy
- `SELECT` - vyberu si, co všechno chci mít v tabulce výsledků, můžu zvolit `*` pro všechno
- `WHERE` - zde můžu používat i `UNION` nebo `AND`
- `FILTER` - filtrování výsledků
	- obsahuje logické výrazy, které pak filtrují výsledky
### Další konstrukce
- `BIND (výpočet) AS (nová_proměnná)` - v rámci dotazu mohu provést nějaký výpočet a výsledek BINDnout na novou proměnnou, kterou můžu pak používat či ji dostat do výsledkové tabulky
- `OPTIONAL` - specifikování nepovinného patternu
	- pokud ho výsledná trojice nesplňuje (a splňuje všechno ostatní mimo OPTIONAL) - do výsledné tabulky se zapíše "NOT BOUND"
- `SERVICE` - způsob, jak se jedním dotazem zeptat přes více [[#SPARQL endpoint]]
- agregace
	- `SUM`, `AVG`, `MIN`, `MAX`, `GROUP BY`, `HAVING` - podobně jako v SQL
- funkce
	- `IF(?x = 2, "yes", "no")` - 1. podmínka, 2. vrátí, pokud je podmínka true, 3. vrátí, když je podmínka false
	- `COALESCE(1/0, ?x, ...)` - vrátí první parametr, který nevyhodí chybu
	- `EXISTS` / `NOT EXISTS` s grafovým patternem na vstupu
- funkce na [[RDF]] termech
	- `isIRI()`, `isBlank()`, `isNumeric()`, `lang()`, `UUID()`, ...
- funkce na stringu
	- `STRLEN()`, `SUBSTR("foobar"@en, 4, 1)`, `CONCAT()`, `REGEX()`, ...
- funkce na číslech
	- `ABS()`, `ROUND()`, `RAND()`, ...
- funkce na datumech a časech
- není možné se dotazovat na blank nodes
### Poddotazy
- jsou možné, vnitřní části se vyhodnotí jako první
```sparql
PREFIX : <https://example.org/>
SELECT ?company ?maxFine
WHERE {
  ?company a schema:Organization .
  {
    SELECT ?company (MAX(?fine) AS ?maxFine)
    WHERE {
      ?company :fine ?fine .
    } GROUP BY ?company
  }
}

```
### Možnosti dotazů
- můžeme se ptát `SELECT`, ale i pomocí:
	- `ASK` - vrací true/false podle toho, jestli dotaz vrátil alespoň jeden výsledek
	- `INSERT` a `DELETE` - umožňují modifikaci dat v [[RDF]] grafu
	- `DESCRIBE` - vrací popis specifikovaného zdroje
	- `CONSTRUCT` - vytváří nový RDF graf ze specifikované šablony
		- jednotlivé výsledky `WHERE` klauzule se vloží do nového výsledného RDF grafu podle šablony 

```sparql
CONSTRUCT
	{ ?s sis:name ?fullName }
WHERE {
	?s sis:firstName ?n1 ;
	sis:lastName ?n2 .
	BIND(CONCAT(?n1, " ", ?n2) AS ?fullName)
}
```