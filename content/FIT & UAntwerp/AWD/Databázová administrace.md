>[!question] Co všechno zahrnuje databázová administrace?
>Je to komplexní služba, která zahrnuje samotnou správu databáze (+ schémata, uživatelé...), zálohy a obnovy záloh, monitoring, správa zabezpečení databáze, proaktivní správa, řešení problémů a bugů, řešení problémů s výkonem a rychlostí...
>

[[Jak funguje databázový systém]]
### Nástroje pro DBA
- buď přes externí nástroje (PGAdmin, PHP Admin)
	- Datagrip (ten je spíš pro developery)
- přes textový editor (a upravování konfiguračních souborů)
- přes bash a bash skripty
- nástroje pro [[PostgreSQL]]:[PostgreSQL: Software Catalogue - Administration/development tools](https://www.postgresql.org/download/products/1-administrationdevelopment-tools/)
##### Nástroj `psql`
- to je klient na bázi terminálu
- nastavení editoru přes environment proměnnou: `PSQL_EDITOR=nano`
- `\?` - výpis všech spešl backslash příkazů
- `\l` - vypsat seznam databází
- `\d` - dump
- `\du` - dump users
- `\x` - prohodí řádky a sloupce (transpozice tabulky)
	- jenom to zapnu/vypnu
- `\q` - quit
- `\dn` - dump namespaces (namespace = schema)
### [[Postgres - administrace]]






 