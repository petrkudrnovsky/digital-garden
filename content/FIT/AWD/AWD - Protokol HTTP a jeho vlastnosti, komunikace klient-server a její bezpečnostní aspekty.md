
> [!tldr] TL;DR
> HTTP (*HyperText Transfer Protocol*) je komunikační protokol pro přenos hypertextových dokumentů a dalších dat mezi klientem a serverem. Jedná se o bezstavový protokol, tedy funguje na principu požadavek -> odpověď. To znamená, že si server mezi jednotlivými požadavky nepamatuje předchozí komunikaci. Uchování stavu (např. přihlášení uživatele či obsah košíku v e-shopu) je pak implementováno jinými cestami (např. [[Cookies]], [[Session]] či tokeny). 
> 
> Požadavek i odpověď mají pevně definovanou strukturu. Hlavička určuje verzi protokolu, konkrétní metodu (GET, POST, PUT, DELETE, PATCH, OPTIONS, HEAD), stavový kód odpovědi a pak sadu hlaviček, které upřesňují konkrétní požadavek či odpověď (autentizační informace, typ a délka obsahu, preferovaná varianta apod.). Mimo standardní hlavičky je možné používat i hlavičky vlastní. Tělo požadavku či odpovědi pak obsahuje samotný obsah zprávy.
> 
> HTTP není šifrovaný a při sledování síťového provozu je možné odchytit citlivé informace. Bezpečnost řeší jeho šifrovaná varianta HTTPS využívající SSL/TLS. Tyto kryptografické protokoly zajišťují důvěrnost a integritu dat.
> 
> Komunikace klient/server je forma výměny informací mezi klientskou částí a serverovou částí aplikace. Klientem je většinou webový prohlížeč nebo mobilní aplikace a server poskytuje požadované služby pro (většinou) více klientů najednou. Pro autentizaci se používá jednoduchý mechanismus HTTP Basic společně s HTTPS. Dále se používají HTTP Digest, API klíče, OAuth, JWT tokeny a další mechanismy.
> 
> Důležitým bezpečnostním prvkem v komunikaci klient/server jsou digitální certifikáty a certifikační autority. Ty zajišťují důvěryhodnost druhé strany a umožní pak bezpečné navázání zabezpečené a šifrované komunikace. 
> 
> Mezi časté bezpečnostní hrozby při komunikaci klient/server patří SQL injection, cross-site scripting (XSS), CSRF (Cross-Site Request Forgery), session hijacking, replay attacks nebo sociální inženýrství. Každý útok vyžaduje jiné mechanismy zabezpečení.
### Rozcestník otázky
[[HTTP protokol]]
- autentizace (HTTP Basic, HTTP Digest): [[Autentizace]]
- [[Autorizace]]

Bezpečnost
- [[Bezpečnost webového serveru]] 
	- [[Certifikát a certifikační autorita]]
- [[Bezpečnost webových aplikací]]

Webový server [[Apache httpd]], tam najdu vše ohledně komunikace klient server
- hlavně
	- [[Zpracování požadavku od klienta na Apache httpd]]
	- [[Moduly pro souběžné zpracování požadavků]]
	- [[Moduly pro práci s MIME informacemi]]
	- [[Generování a úprava obsahu v Apache httpd]]
	- [[Autentizace a autorizace v rámci Apache httpd]]

Celkově témata předmětu AWD:
- [[AWD - administrace webového a databázového serveru]]
