- získávání přehledu o aktuálním výkonu, identifikace problémů a jejich odstranění
- součást [[Databázová administrace]]
- sledujeme:
	- výkon dotazů
	- zátěž databáze
	- logování aktivit
- podle výsledků monitoringu děláme [[Postgres - optimalizace a tuning|optimalizace a tuning]]
### Výkon dotazů
- chceme najít dotazy, které trvají velmi dlouho
- nebo které se provádějí velmi často a i malinká [[Postgres - optimalizace a tuning|optimalizace]] bude mít velkej vliv
- pro analýzu používáme příkazy `EXPLAIN` a `EXPLAIN ANALYZE` (viz [[Postgres - optimalizace a tuning#Prováděcí plány a statistiky]])
### Zátěž databáze
- kolik se provede transakcí, kolik je připojených klientů, jaké prostředky se používají
- jak různé (připojené) aplikace ovlivňují databázi
- nástroj pro generování zátěže: `pgbench` 
	- je to implementace TPC-B benchmarku (měření transakční zátěže - simulace bankovního systému)
### Logování aktivit
- víme, co přesně se děje
- logování připojení/odpojení klientů atd.
- logování nastavujeme v `postgresql.conf`
	- `logging_collector = on`, nastavíme i formát, cílový soubor
	- speciální logy pro dotazy, které trvají více než X milisekund
	- logování připojení/odpojení
	- logování [[Write Ahead Logs (WAL)#Událost checkpoint|checkpointů]]
- nástroj pro analýzy logů: `pgBadger`
	- vizualizuje logy a tvoří reporty (po analýze logů vytvoří [[HTML]] stránku s přehledy) + identifikuje problémy