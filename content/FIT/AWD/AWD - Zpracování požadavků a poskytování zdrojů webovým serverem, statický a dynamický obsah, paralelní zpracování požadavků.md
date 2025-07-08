
> [!tldr] TL;DR
> Webový server přijímá a zpracovává HTTP(S) požadavky od klientů a poskytuje jim příslušné zdroje. Každý požadavek směřuje na konkrétní URL zdroje a je zpracován podle nastavených pravidel.
> 
> Zpracování požadavku je proces, který zahrnuje následující kroky: příjem požadavku, předzpracování (např. pomocí vstupních filtrů), kontrolu přístupu (autentizace a autorizace klienta), rozhodnutí o způsobu zpracování (např. přímý přístup ke statickému souboru či předání požadavku handleru), generování odpovědi, aplikaci výstupních filtrů (např. komprese či vodoznak) a odeslání výsledku zpět klientovi.
> 
> Statický obsah zahrnuje souboru uložené na serveru, které se nemění (např. HTML, obrázky, CSS, JS). Webový server je může přímo odeslat bez žádného dalšího zpracování.
> 
> Dynamický obsah se generuje za běhu podle kontextu požadavku. To zajišťují různé mechanismy, jako:
> - CGI (Common Gateway Interface) - rozhraní pro externí skripty (v PHP, Perlu apod.), které generují odpověď
> - SSI (Server Side Includes) - vkládání dynamických částí do statického HTML souboru (např. header, footer, datum apod.)
> - Actions - mechanismus, který na základě MIME typu souboru rozhodne o skriptu, který daný soubor obslouží
> 
> Lze také využít tzv. vyjednávání obsahu, kdy může server klientovi vybrat správnou verzi zdroje podle požadavků klienta (např. konkrétní jazyk, formát či kódování). Toto zajišťují moduly *Type-map* a *Multiviews*.
> 
> Aby bylo možné efektivně zpracovávat více požadavků najednou a škálovat, je potřeba implementovat paralelní zpracování požadavků. To zajišťují moduly:
> - *prefork* - pro každý požadavek vytvoří samostatný proces 
> - *worker* - v rámci jednoho procesu využívá více vláken
> - *event* - odděluje vlákna pro příjem a pro zpracování požadavků, čímž efektivněji pracuje s pomalými připojeními
### Rozcestí otázky
Hlavní informace o chodu webového serveru: [[Apache httpd]]
- pak hlavně:
	- [[Generování a úprava obsahu v Apache httpd]]
	- [[Zpracování požadavku od klienta na Apache httpd]]
	- [[Moduly pro souběžné zpracování požadavků]]
	- [[Moduly pro práci s MIME informacemi]]

Celkově témata předmětu AWD:
- [[AWD - administrace webového a databázového serveru]]