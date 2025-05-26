## [[Testování aplikace]]
- typologie testů
- black, white, grey testy
- automatizace a proces testování v [[Continuous Integration (CI) a Continuous Delivery (CD)]]
## [[Statická analýza kódu]]

## Code review
- manuální kontrola kódu někým jiným než je autor (většinou v rámci MR, PR)
- umožňuje sdílet znalosti v týmu
- provádí se až po [[Statická analýza kódu]] - pro uspoření času kontrolujícího člověka
- je možné vkládat komentáře, navrhovat změny a označovat si zkontrolované soubory
## Zranitelnost aplikací
- OWASP TOP 10 - 10 nejzávažnějších bezpečnostních chyb, které se objevují ve webových aplikacích -> [[Bezpečnost webových aplikací]]
- další bezpečnostní rizika
	- broken access control
		- tím, že změním URL se dostanu tam, kam nemám a zjistím to, co nemám
	- cryptographic failures
		- citlivá data se posílají přes nešifrované kanály (http)
		- neověřuje se platnost [[Bezpečnost webového serveru#Certifikáty a řetězec důvěry|certifikátu]] 
		- zálohy jsou uložené nešifrovaně
	- [[Bezpečnost webových aplikací#SQL Injection|SQL injection]]
	- injection
		- generuje se HTML, kam může uživatel něco vložit - škodlivý skript
	- insecure design
		- slabiny, které vychází s designu aplikace (ne z implementace)
		- nezabezpečení proti botům, obnovení hesla pomocí otázek apod.
	- security misconfiguration
		- po chybě se vypisuje stack trace
		- nezměnění defaultních hesel
	- vulnerable and outdated components
- CWE (Common Weakness Enumeration)
	- databáze typů bezpečnostních problémů
- CVE (Common Vulnerabilities and Exposures)
	- databáze zranitelností v konkrétních knihovnách
	- je potřeba ověřovat i zranitelnosti v knihovnách, je možné ohrozit aplikaci použitím zranitelné knihovny
		- je dobré používat nástroje pro správu balíčků a knihoven (Maven, Gradle, npm atd.), které udržují přesnou verzi knihovny (a mohou ověřit zranitelnosti v této verzi - např. Dependency check od OWASPu)
		- [[Balíčkovací systémy a sémantické verzování]]

