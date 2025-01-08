- nejpopulárnější [[Grafové databáze|grafová databáze]]
- open-source, implementovaný v Javě

Neo4j je tzv. "Multilabeled Property Multigraph"
- tedy každý ==vrchol== a hrana mohou mít různé atributy
	- štítky (labels) určují datový typ
	- ==hrany== ještě mají typy (jednosměrné, obousměrné, bez rozlišení)
	- ==vlastnosti== - reprezentují metadata (klíč-hodnota)
- *každá hrana může mít jiná metadata a jiné popisy a díky tomu mohu definovat komplexní vztahy mezi objekty a každý vztah si mohu zvlášť nadefinovat*

Plně podporuje transakce a [[ACID]]
- *atomicity, consistency, isolation, durability*
- konzistence a integrita dat v grafu je tím zachována
- díky transakci mohu změnit více dat na grafových datech najednou a přitom zajistit, že jsou data vždy v konzistentním stavu

Podporuje horizontální škálování a umožňuje rozdělení dat na více serverů (+ paralelení zpracování)
- ale nedají se horizontálně škálovat tak dobře jako jiné [[NoSQL databáze]]

Vyniká v grafově orientovaných dotazech, moc dobře neumí agregační funkce

Datové typy nejsou nijak specifikovány - Neo4j si je určí sama

Rozšíření
- Neo4j Browser - rozhraní pro dotazování a vizualizace, správce dat
- Neo4j Desktop - nástroj pro správu a vývoj databáze

Aktivní komunita neustále přidává další pluginy a rozšíření + Neo4j podporuje integraci s hlavními programovacími jazyky
### Nevýhody
- shardování grafových dat je velmi složité, tak je horizontální škálování omezenější než např. pro [[Apache Cassandra]], jde to, ale není to příliš doporučené
- dále neumí dobře agregovat data
- nemá tak vyvinuté transakce než RDBMS

Lze řešit různé grafové problémy pomocí různých grafových algoritmů
- shortest path
- all paths
- Dijkstra - nejlevnější cesta mezi 2 uzly
- A\* - optimalizační úlohy
### Vlastní dotazovací jazyk Cypher
- deklarativní jazyk speciálně vytvořený pro práci s grafovými daty
- sekundární jazyk se jmenuje Gremlin - umožňuje procházení grafu (imperativní)
- používá ASCII art pro dotazování (šipečky, vizuální znázornění vrcholů a hran)
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