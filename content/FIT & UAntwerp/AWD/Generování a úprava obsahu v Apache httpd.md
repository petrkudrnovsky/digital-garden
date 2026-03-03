- v rámci Apache httpd mohu vytvářet, zpracovávat a měnit obsah před tím než se odešle klientovi
## Externí filtry (mod_ex_filter)
- filtrování vyprodukovaných souborů zajišťuje externí modul nebo program
- filtry mohou měnit či upravovat obsah odchozích souborů - hodí se to spíš na menší a jednorázové úpravy
	- např. doplnění vodoznaku, zmenšení obrázku, vyhledání a nahrazení nějakého textu, přidat komentáře apod.
- jednotlivé filtry mohu za sebou řetězit
## CGI moduly (mod_cgi a mod_cgid)
= *common gateway interface*
- moduly, které zajišťují běh externích skriptů (napsaných v různých jazycích - Perl, Python, C...)
	- procesové zpracování požadavků ([[Moduly pro souběžné zpracování požadavků#prefork]])
	- vláknové zpracování požadavků ([[Moduly pro souběžné zpracování požadavků#worker]] či event) 
- definuji si adresář, ve kterém se soubory budou zpracovávat jako skripty (`ScriptAlias`)
##### Jak to funguje?
- uživatel pošle požadavek na stránku ovládanou CGI skriptem, server předá požadavek a jeho data skriptu a pak výsledek skriptu vrátí zpět uživateli
	- webserver předá hlavičky, data v těle požadavku a nastaví proměnné prostředí (envvars) - tedy kontext pro daný skript, aby měl s čím pracovat
	- skript pak vygenerovaný obsah předá zpět webovému serveru, který ho pak ještě před odesláním zpět může nějak upravit
- skripty, které generují odpovědi pro uživatele/klienty, tak musí správně generovat hlavičku (a třeba `Content-Type`, aby bylo poznat, co bylo vygenerované)
	- to, co skript vygeneruje (např. HTML stránka, se předává zpět klientovi)
- skript má přístup k proměnnám prostředí, k obsahu hlaviček i k datům (např. u [[REST protokol#POST|POST]]) 
## SSI (Server Side Includes) (mod_include)
- vkládá dynamicky generovaný obsah do výstupu
- hodí se spíš, když do stránky chci přidat nějaké menší kusy dat - pokud je většina stránky generovaná dynamicky, tak je spíš lepší použít jiný přístup
	- např. headery, footery, datumy a další opakující se prvky
- zapisujeme SSI direktivy pomocí HTML komentářů, které server interpretuje před tím, než odešle odpověď klientovi
```
<!--#include file="footer.html" -->
<!--#echo var="DATE_LOCAL" -->
<!--#exec cmd="/usr/bin/uptime" -->
```
- `file` - pouze pro soubory z aktuálního adresáře nebo podadresáře
- `virtual` - dokument s adresou ve webspace serveru (`DocumentRoot`)
##### Co umí?
- vkládat HTML soubory do jiných (includovat) - headery, navy, footery
- zobrazení dynamických dat - čas, datum
- podmíněné zobrazování - např. podle IP adresy klienta

Výhoda je jednoduchost a nižší nároky (než třeba PHP či Python) - hodí se na víceméně statické stránky s pár dynamickýma částma nebo menší weby, kde není nasazený plnohodnotný aplikační server.
## Actions (mod_actions)
- jedná se o mechanismus, který na základě MIME typu souboru či přípony souboru rozhodne, jaký skript daný soubor obslouží
	- např. všechny `.php` soubory se obslouží tímto specifickým skriptem
	- v podstatě, když třeba klient žádá nějaký specifický soubor, tak se spustí určený skript, který může dělat cokoliv
```
LoadModule actions_module modules/mod_actions.so

<Directory "…">
  # textové soubory dostanou spec. hlavičku a patičku
  Action text/plain "/actions/txt.sh" virtual
</Directory>
```
- slovo `virtual` řeší neexistenci souboru a říká, že i když soubor neexistuje, tak je to v pohodě a spoléhá se, že skript se s neexistencí souboru popasuje sám (např. ho vytvoří)
- skript musí určitě dodat hlavičku `Content-Type`, zbytek hlaviček může dodat/dopočítat server
- stejně jako [[#CGI moduly (mod_cgi a mod_cgid)|CGI skript]], tak má tento skript přístup k proměnným prostředí 

Můžu si tím vytvořit interpret pro vlastní formáty nebo speciální obsluhu nějakého [[MIME type]] formátu

> [!tip] TL;DR
> Actions se chovají v podstatě stejně jako CGI skripty. Akorát pro CGI skripty klient musí poslat něco na specifickou URL, aby se skript vykonal, tak pro Actions to funguje tak, že vlastně "poslouchají" a jakmile klient zažádá o zadaný typ souboru, tak přesměrují zpracování na zadaný skript.