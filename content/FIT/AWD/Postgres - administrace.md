[[Databázová administrace]] - obecně
[[PostgreSQL]] - obecně
### Nástroje pro administrátory Postgresu
- pgAdmin - nejznámější a nejpoužívanější GUI
	- jsou to vlastně jenom vizualizované pohledy do ==Data Dictionary==
		- to je speciální struktura/sada tabulek pro metadata o databázových objektech a jejich stavu
		- poskytuje klíčové informace pro správu a optimalizaci databáze
- Datagrip - spíše pro developery než adminy
- Tableplus
- [[DBeaver]]
***
### Uživatelé a role
- v Postgresu neexistují uživatelé v pravém slova smyslu - jsou to Role s atributem `LOGIN`
	- `CREATE ROLE student WITH LOGIN PASSWORD 'heslo123';`
	- další atributy Rolí jsou např. `SUPERUSER` nebo `CREATEDB`
- příkazy `GRANT` a `REVOKE` - odebírají a přidávají atributy k rolím (správa práv jednotlivých uživatelů)
- uživatel existuje na úrovni clusteru - může vlastnit databázi (těch může být v jednom clusteru více)
- dědění rolí - lze pomocí dědění vytvářet hierarchii
### Vytváření databází
- vytvoření databáze je rychlý a levný proces
- standardně se kopíruje z `template1` (viz [[PostgreSQL#Rozložení dat a tabulek]])
	- můžu si upravit rovnou `template1`, abych dané úpravy měl v každé nové databázi
	- pomocí klíčového slova `TEMPLATE` ale mohu vybrat šablonu podle které se databáze vytvoří
		- můžu si vytvořit nové užitečné funkce a nastavení a každá další nově vytvořená databáze tyto úpravy zdědí
	- šablona `template0` je vhodná pro vytvoření čisté databáze bez žádných přidaných prvků
### Schémata
>[!question]- Co je schéma databáze?
>Je to způsob, jak logicky oddělit objekty v databázi. Každý objekt (tabulka, pohled, funkce) je uložený v konkrétním schématu. Funguje to jako namespaces v programovacím jazyku. Defaultní schéma v Postgresu se jmenuje `public`
- `search_path` - session proměnná, která udává výchozí schéma (do toho se vytvoří aktuálně vytvořený objekt)
	- `SET search_path TO studentske_schemata;
	- `SHOW search_path;`
### [[Postgres - optimalizace a tuning#Tablespaces|Tablespaces]]
> [!question]- Co jsou tablespaces?
> Umožňují ukládat části databáze na specifická místa na disku nebo na různé disky. To se používá hlavně pro výkonové optimalizace.
### [[Autentizace]] a [[Autorizace]]
- nastavuje se v souboru `pg_hba.conf`
```conf
# TYPE   DATABASE USER ADDRESS   METHOD
  host   all      all  0.0.0.0/0 scram-sha-256 
  local  all      all            peer
```
- když tam je type `host` - připojuju se přes TCP/IP (viz [[Jak funguje databázový systém]]) - proto je tam zabezpečení `scram-sha-256`
- když tam je type `local` - připojuju se přes sockety (přes SSH)
	- ale databázový server musí být na stejném stroji jako uživatel
- metody
	- `peer` - přihlásí mě to do PG podle toho, jak jsem přihlášenej v rámci OS
	- `trust` - kdokoliv si řekne, je připuštěn
	- `scram-sha-256` - metoda posílání hesla, zabezpečené

- jak zpřístupnit PG pro připojení zvenčí?
	- změnit `listen_adresses` na `'*'` - default je `localhost`
		- v konfiguraci `postgresql.conf`
		- a změnit IPv4 na `0.0.0.0/0`
- každý cluster musí běžet na jiném portu (default je 5432)
- `.pgpass` - hesla jsou uložená v plaintextu (musí mít upravená přístupová práva)
	- není to bezpečácky uplně košer, ale admini a vývojáři to rádi používají
		- na produkci se to pak musí zabezpečit (používat silná hesla a omezit přístup k databázi pomocí konkrétních IP adres)
	- tedy nemusím psát heslo, `psql` si to sám vezme z toho souboru
### [[Postgres - architektura]]
### [[Postgres - záloha a obnovení]]

##### Jak importovat a exportovat data do/z PG?
- pomocí příkazu `COPY` nebo `\COPY` v [[Databázová administrace#Nástroj `psql`|psql]] 
### [[Postgres - monitoring]] a [[Postgres - optimalizace a tuning]]
- je to iterativní proces
- 1) monitoruji výkon a indetifikuji problémové dotazy a oblasti
- 2) aplikuji optimalizace a ladění (tuning)
### [[Postgres - extensions]]
### [[Postgres - logická replikace]]