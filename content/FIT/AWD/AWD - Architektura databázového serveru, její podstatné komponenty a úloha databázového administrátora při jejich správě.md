> [!tldr] TL;DR
> Databázový server lze rozdělit na dvě hlavní komponenty. První částí je samotná databáze (obsahující samotná data, zálohy, statistiky, indexy a další interní struktury). Tato část není přístupná přímo, ale pouze prostřednictvím druhá komponenty. Přístup k databázi zajišťuje databázový cluster. Jedná se o běžící instanci, která zajišťuje připojování klientů a jejich komunikaci s databázovou částí. 
> 
> Databázový cluster (v případě PostgreSQL) standardně naslouchá na portu 5432 a klienti se připojují pomocí TCP/IP spojení a komunikují pomocí SQL. V clusteru běží hlavní proces *postmaster*, který přijme nové připojení klienta (jedná se o klient/server architekturu), provede autentizaci a poté pro klienta vytvoří nový podproces *postgres*. Tento proces udržuje spojení s klientem a vykonává jeho požadavky. Mezi důležité části databázového clusteru patří také sdílené mezipaměti a WAL logy pro rychlejší běh serveru a lepší konzistenci dat.
> 
> Hlavním konfiguračním souborem PostgreSQL databázového systému je *postgresql.conf*, který nastavuje parametry pro velikosti paměti, port, na kterým server naslouchá či logování. Konfigurační soubor *pg_hba.conf* zajišťuje přístupovou kontrolu (tj. kdo a jak se smí k serveru připojit).
> 
> Databázový administrátor spravuje samotný databázový server. To zahrnuje konfiguraci serveru, správu databází, schémat uživatelů a jejich oprávnění. Provádí a nastavuje zálohy a obnovy záloh. Spravuje zabezpečení databázového serveru a nastavuje přístupová práva. Monitoruje výkon a rychlost databáze a identifikuje problémová místa (pomalé dotazy, neefektivní indexy) a optimalizuje běh databáze. Řeší také problémy a chybové stavy během provozu, spolupracuje při nasazování nových verzí databáze a podílí se na aktualizacích.
### Rozcestník otázky
Jaká je architektura databázového serveru:
- [[Jak funguje databázový systém]]
- [[Postgres - architektura]]

Jak obecně funguje databázová administrace:
- [[Databázová administrace]]
- [[Postgres - administrace]]
	- obsahuje všechny části administrace databázového serveru (monitoring, optimalizace, tuning, zálohy, logická replikace, rozšíření)

Obecný rozcestník: [[PostgreSQL]]
