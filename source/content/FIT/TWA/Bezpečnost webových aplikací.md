### Jak předejít útokům?
- myslet na bezpečnost při návrhu
- detekování hrozeb: logovat a sledovat logy
- je potřeba zabezpečit všechny kanály (síť, hostitel, aplikace...)
- dvakrát myslet na ošetření vstupu od uživatele

útok - je skutečná akce, že někdo přijde a provede útok
zranitelnost - chyba v aplikaci, která je předpokladem pro hrozbu

[OWASP Foundation](https://owasp.org/) zveřejňuje statistiky o nejčastějších útocích na webové aplikace
- je tam hodně článků, které se věnují zabezpečení
	- např. jak dlouho má být aktivní "Zapamatovat si" tlačítko
	- https://cheatsheetseries.owasp.org/index.html - super cheatsheety na všemožné webdev věci (HTML, JS, PHP, Symfony atd. atd.)
### SQL Injection
- útočník může zjistit celou architekturu aplikace (hlavně databáze) - podle chyb, které DB vyhazuje
- může získat nebo poškodit důležitá data (smazat tabulky)
- ochrana
	- validovat vstupy od uživatele, kontrolovat dat. typy, escapovat správně
	- nejlépe používat předpřipravené dotazy (viz níže)

V `php.ini` je nastavení i o tom, jak se mají zobrazovat chyby - pro vývojáře jsou důležité podrobné informace, pro produkci nikoliv

Přepřipravené dotazy - pošle se dotaz se zástupnými znaky a pak až se pošlou data (které si tam pak ten DATABÁZOVÝ server escapuje a dosadí)
- do zkoušky: tohle je ta nejlepší metoda, jak to udělat :))
### Remote code execution
- většinou u dynamicky typovaných jazyků
- lze u nich spustit kód, který je ve stringu (např. funkce eval())
	- není problém ji použít, ale ne na vstupy od uživatele
- u příkladu je častý způsob, jak zapisovat použití controlleru tak před 10 lety
	- hledání správných souborů pro include přes $GET
	- tedy přidávání knihoven za běhu (pomocí include nebo require)
- také pozor na použití procesů (exec(), pcntl_open())
- ochrana
	- neumožnit uživateli spouštět jeho kód
	- pokud je to nutné, tak vybrat seznam možností spuštění a nenechat ho spouštět cokoliv
### Cross-Site Scripting (XSS)
- vkládání škodlivého skriptu do aplikace, což se pak ostatním uživatelům spustí, může jim to něco (ne)zobrazit, změnit, upravit, smazat apod.
- vkládání škodlivého JS skriptu do proměnné, která se pak zobrazí (tím pádem i spustí)
- druh Javascript injection
- dočasné chyby
	- většinou součástí URL, Cookie
	- ohrozí jen konkrétního uživatele
	- podvodný odkaz je na validní stránku, ale v query parametru se nachází něco, co se někam vloží (např. do vyhledávání apod.)
- perzistentní chyby
	- uloží se do databáze a tím může ohrožovat všechny uživatele systému
- je potřeba neošetřovat jenom vstupy, ale i výstupy (abych nezobrazil, co nechci - nějaké citlivé informace)
	- ten skript totiž může zobrazit ostatním uživatelům něco, co já nechci
	- nepoužívat echo, ale Twig apod.
- zakázat vykonávání skriptů
### CSRF
- Cross-Site Request Forgery
- využívá stránky, na kterých jsem přihlášený - aplikace nám věří, že jsme tím, za koho se vydáváme
	- nutí nás provést nežádoucí akce na skutečné aplikaci, kde jsme přihlášení
- dobrá kombinace s XSS (problém i s POST metodou)
	- můžu tam místo odkazu URL dát fetch
- server má CSRF token v session
- je nutné, aby platil jenom omezenou dobu, např. 5min
	- problém, když nějaký formulář vyplňují uživatelé hodinu
	- pak 1. část je bez CSRF tokenu a pak např. shrnutí a jednoduché odeslání už s CSRF tokenem
- Referer = v hlavičce, obsahuje předchozí stránku, ze které jsme přišli
	- se hodí na debug 404 - můžu se podívat, kde mám třeba nevalidní odkaz
	- kontrola této hlavičky
- ochrana pomocí více-faktorového ověření
- Anti-CSRF tokeny u formulářů
	- jako u prevence vícenásobného submitnutí formuláře
### Session stealing
- problém, když mám špatný session id generátor (nebo také IDčka uživatelů)
- pozor, když musím mít ID uživatele v URL - tak je lepší použít UUID spíš
- stealing
	- pomocí XSS JS pošle požadavek na útočníkův server se SessionID v těle
	- útočníkův server dostane SessionID
- cooking
	- útočník vytvoří a nastaví uživateli konkrétní "session ID" a uživatel je pak nucen tohle ID používat (to samozřejmě zná i útočník)
- ochrana
	- dlouhé náhodné hodnoty
	- obnovovat session ID pravidelně
	- pokud server SessionID nemá, zamítne a vygeneruje svoje vlastní
		- akceptovat hodnoty pouze vygenerované serverem
	- pokud není šifrovaná komunikace => útok man in the middle
	- pomocí HTTPS je potřeba zabezpečit celý web (ne jenom přihlášení a objednávky)
		- protože na HTTP stránkách už se sice neposílají údaje, ale SessionID pořád
	- hlavička Referer, IP adresa, User-Agent, nonce <- kontrolovat
### Replay attack
- jakmile zjistím, že nějaký požadavek je validní, tak ho opakuji
- vím, že trvá dlouho (potřebuje více zdrojů) => DoS 
	- firewall - detekce DDoS útoků
### Sociální inženýrství
- nejslabší článek je uživatel a na toho tyhle útoky cílí
- nějaké obalamutění uživatele, aby sdílel citlivé údaje