+ doplnění otázky: (principy řešení spolupráce, hlavní přínosy, používané nástroje)
# Přehled nástrojů pro podporu tvorby SW
- [[#Wiki nástroje]] - pro efektivní sdílení důležitých informací k projektu na jednom místě
- [[#Správa úkolů, požadavků a chyb]]
- hodí se pro [[Projektové řízení]], [[Týmový projekt]], [[Agilní přístup]]
## Wiki nástroje
- dokumentace, informace, doprovodné dokumenty
	- základní informace o projektu, kontakty, zápisy ze schůzek
	- popis architektury/řešení
	- single source of truth pro informace ve firmě
- existují jako samostatné nástroje (Confluence), často jsou součástí nástrojů pro správu verzí zdrojového kódu (Gitlab Wiki)
- pro strukturu informací se používají značkovací jazyky
	- Markdown, AsciiDoc, HTML
	- nebo WYSIWYG editory
- příklady
	- Gitlab Wiki - v rámci jednoho projektu mám více repozitářů (a jeden z nich je pro Wiki)
	- Confluence - v synchronizaci s Jirou
	- Notion
	- MediaWiki - open-source
## Správa úkolů, požadavků a chyb
- pro plánování práce na projektu, vyhodnocování plnění plánu, organizace týmu
- potřebné úkony
	- evidence úkolů
	- přidělování úkolů
	- plánování a deadliny úkolů
	- kontrola splnění úkolu
- nástroje pro správu úkolů
	- Gitlab Issue Management
	- Mantis
	- Bugzilla
	- Jira
	- Redmine
#### Úkol
- identifikátor - pro identifikaci a rychlé odkazování na úkol
- název
- popis - detailní popis požadavku/úkolu/chyby + případné odkazy do Wiki
- milestone - určuje datum, do kterého mají být dokončené vybrané úkoly
- priorita
- severita - jak velký je dopad na uživatele
- odhad pracnosti
- řešitel
- vykazování skutečně odpracovaného času (time tracking)
- stav (podle životního cyklu projektu)
###### Životní cyklus úkolu
![[Pasted image 20250331150444.png]]
- úkol může být vyřešen různými způsoby:
	- Hotovo/opraveno (fixed)
	- Duplikace (duplicate)
	- Nebude opraveno (won't fix)
	- Nevalidní (invalid)
## Správa zdrojových kódů
- pro sdílení souborů a zdrojových kódů v rámci celého týmu
- umožňují verzování souborů 
	- možnost se vrátit ke starším verzím
	- možnost vidět kompletní historii úprav
- omezení rizika ztráty dat
	- pokud se soubory poškodí nebo smažou na lokálním počítači, je možné je obnovit
	- pokud se nahrává na vzdálený repozitář, tak můžeme data obnovit i po ztrátě/poškození disku
- nástroje: [[GIT]] a Mercurial
#### Typy systémů pro správu verzí zdrojových kódů
- centralizované
	- všechny verze/revize souborů jsou uložené pouze v centrálním repozitáři (na serveru)
	- vývojář má u sebe pouze pracovní verzi (pouze část repozitáře)
	- veškeré verzování probíhá online
	- nástroje:
		- SVN (Apache Subversion)
		- CVS
- distribuované
	- celý repozitář existuje naklonovaný na zařízení každého vývojáře
	- vzdálený repozitář funguje pro sdílení práce mezi členy týmu a spouštění CI/CD
	- je možné verzovat pouze lokálně
	- pak probíhá synchronizace změn mezi lokálním a vzdáleným repozitářem
#### Způsoby řešení spolupráce
- zamykací režim
	- pokud někdo daný soubor upravuje, nikdo jiný ho nemůže upravovat
	- využití pro soubory, které nelze slučovat po částech (tedy netextové soubory)
	- využívá se pouze v rámci centralizovaných systémů (SVN)
- slučování změn
	- pro textové soubory (a zdrojové kódy)
	- pokud dojde k souběžným úpravám jednoho souboru, je možné tyto úpravy sloučit
		- manuálně (conflict) nebo automaticky 
	- podpora v SVN i v [[GIT]]
#### GIT workflows
- dohoda, jakým způsobem budou spravovány jednotlivé větve v repozitáři
	- [[GIT#Git Flow]]
	- [[GIT#Github Flow]]
	- [[GIT#Gitlab Flow]]
	- [[GIT#One Flow]]