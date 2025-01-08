- dodatečné balíčky, které dodávají nové funkce do Postgresu
- jsou jich tisíce, ty nejzákladnější jsou nainstalované v základu

- `SELECT * FROM pg_available_extensions;` - jaké extensions jsou dostupné
- `CREATE EXTENSION pg_stat_statements;
### PL/pgSQL 
- rozšíření funkcionality pro [[Function vs. Procedure vs. Trigger v kontextu databází|procedury a funkce]] 
- je to takový programovací jazyk pro Postgres (PL = procedural language)
	- umí vytvářet procedurální funkce a triggery - tedy rozšíření SQL o logiku, cykly, podmínky apod.
	- takže můžu vytvářet komplexnější databázový appky
### pg_stat_statements
- sleduje výkony SQL dotazů (frekvence, délka vykonání atd.) - obsahuje vyhodnocení nedávných SQL dotazů (jaký byl prováděcí plán s cenou a jak to dopadlo)
- tabulky `pg_stat*`
### FDW (Foreign Data Wrappers)
- mohu z postgresu přistupovat k jiným datům a objektům z externích datových zdrojů (MySQL, Oracle, soubory atd.)
- můžu pak číst a někde i zapisovat data jako kdyby byly v postgresí databázi
- existují různé wrappery na různé datové zdroje