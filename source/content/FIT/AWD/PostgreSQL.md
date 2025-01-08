- moderní, open-source ORDBMS (Object Relational DataBase Management System)
	- implementuje SQL standardy
- vznik v Berkeley
- samotný PostgreSQL už umí i pracovat s JSONama (filtrovat, vyhledávat) 
	- jedná se o nadstavbu nad relační databáze a SQL
	- není potřeba tedy hned nahrazovat [[MongoDB]] databází

>[!info] Aktuální zdroje informací
>- [PostgreSQL: Documentation: 17: PostgreSQL 17.0 Documentation](https://www.postgresql.org/docs/current/)
>- [PostgreSQL wiki](https://wiki.postgresql.org/wiki/Main_Page) - také spravované PostgreSQL

[[Postgres - administrace]]
[[Postgres - architektura]]
[[Postgres - monitoring]]
[[Postgres - optimalizace a tuning]]
[[Postgres - extensions]]
[[Postgres - logická replikace]]
[[Postgres - záloha a obnovení]]

>[!info] Hlavní konfigurační soubory
>- `postgresql.conf` - hlavní konfigurační soubor, načítá se při startu
>	- konfigurace instance/clusteru (viz [[Jak funguje databázový systém]])
>- `pg_hba.conf` - hlavně přístupová pravidla ([[Autentizace]]/[[Autorizace]])
>	- jací hosti se mohou připojit
>	- jak se klienti autentizují, k jakým databázím mají přístup atd.
>	- rozlišuju local/host
>	- local - autentizuju se SSH (jedu přes sockety, ne přes TCP/IP)
>	- host - přes TCP/IP
>- authentication_method - používá se šifrování md5 (hlavně, ale jsou i další)
>	- trust - je to otevřený (bez hesla), hodí se na `localhost`
>- jejich uložení se dá zjistit přímo z `psql` příkazem `SHOW CONFIG`
