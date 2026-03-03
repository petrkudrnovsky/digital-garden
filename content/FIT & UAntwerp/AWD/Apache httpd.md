Open-source, je zdarma a je to takový "švýcarský nůž" na webové servery
- umí skoro všechno, podporuje spoustu jazyků, modulů atd.
- v jednotlivých směrech většinou není nejlepší (existuje lepší víceúčelový nástroj)
- je univerzální a nemusím se pořád učit něco nového a jiného
## Architektura
![[Pasted image 20250108190103.png]]
APR (Apache Portable Runtime) neboli prostředí pro běh serveru
- mezivrstva mezi Apachem a konkrétním operačním systémem
- nemusím řešit JAK posílat packety, jak navázat spojení atd. - což je na každém OS jiný
- řeší využití různých periferií atd.
## Dokumentace
Webová varianta: [Documentation: Apache HTTP Server - The Apache HTTP Server Project](https://httpd.apache.org/docs/)
Konkrétní dokumentace pro moji aktuální verzi se nachází přímo v souborech toho webového serveru ([[HTML]] stránka)
## [[Zpracování požadavku od klienta na Apache httpd]]
## Instalace
- webový server můžeme nainstalovat
	- binárně
		- tedy pomocí balíčkovacích systémů (`apt, yum, pkg...`)
		- jednodušší a snadnější, předkompilovaná verze Apache httpd
		- jsem odkázán na to, co nabízí balíčkovací systém
	- ze zdrojových kódů
		- náročnější, dělám si to sám, můžu hodně ovlivnit, jaká součásti se nainstalují, můžu si to celé nakonfigurovat apod.
		- musím si to zkompilovat sám
		- nové verze si musím hlídat sám
		- 1) konfigurace, pak 2) kompilace a pak 3) instalace
			- `./configure && make && make install`
## [[Konfigurace Apache httpd]]
## Ovládání Apache
- pomocí wrapperu `apachectl` - start, stop, test, reload, restart...
	- umí také zkontrolovat konfigurační soubor samostatně
	- je to jenom wrapper na příkaz `httpd`, kterému pak všechny argumenty předává
	- můžu s ním zobrazit statické (zkompilované) a dynamické moduly
- Debian má nástroje `a2*` (např. `a2query` nebo `a2enconf`) pro ovládání
## [[Direktivy pro Apache httpd]]
## [[Moduly pro Apache httpd]]
- [[Moduly pro práci s MIME informacemi]]
- [[Moduly pro souběžné zpracování požadavků]]
## [[Dojednávání obsahu v Apache httpd]]
## [[Generování a úprava obsahu v Apache httpd]]
## [[Autentizace a autorizace v rámci Apache httpd]]
## [[Bezpečnost webového serveru]]

## Adresářová struktura v Debianu
- pro další OS se liší

| `/etc/apache2/`                | globální konfigurace                           |
| ------------------------------ | ---------------------------------------------- |
| `/etc/apache2/mods-available`  | konfigurace modulů                             |
| `/etc/apache2/sites-available` | konfigurace virtuálních hostitelů              |
| `/etc/apache2/conf-available`  | rozšiřující konfigurace                        |
| `/usr/lib64/apache2/`          | zkompilované knihovny                          |
| `/usr/lib64/apache2/modules`   | zkompilované moduly                            |
| `/usr/sbin`                    | binární soubory                                |
| `/usr/share`                   | dokumentace, manuálové                         |
| `/usr/share/man`               | stránky, příklady                              |
| `/var/log/apache2`             | logy                                           |
| `/var/www`                     | webové stránky                                 |
| `/var`                         | v dalších podadresářích další soubory, zámky … |
