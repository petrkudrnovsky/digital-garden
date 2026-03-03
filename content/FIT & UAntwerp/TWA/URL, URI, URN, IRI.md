## URI
= *uniform resource identifier*
- identifikuje/pojmenovává zdroje na internetu
	- existující URI neznamená, že zdroj opravdu fyzicky existuje
- syntaxe je specifická podle zvoleného schématu
	- příklady schémat: `http`, `ftp`, `mailto`, `info` - schéma, které označuje záznamy v záznamovém katalogu 
- může mít aliasy (tj. různé URI, které identifikují stejný zdroj)
### IRI
= *Internationalized Resource Identifier*
- zobecnění [[#URI]], umožňuje používat Unicode místo ASCII
### URL
= *uniform resource locator*
- specifikuje UMÍSTĚNÍ zdrojů na internetu nebo obecně v síti
	- například: lokace souboru/stránky na internetu
- podmnožina [[#URI]]
- jeho schéma: `scheme:[//authority]path[?query][#fragment]` 
	- schéma se NEROVNÁ protokol
	- autorita - registrované doménové jméno nebo adresa serveru
		- může obsahovat i uživatele a port
	- path - hierarchická, slouží k identifikaci zdroje v kontextu daného schématu a autority
		- depends on the app specific data model a jak organizuje svoje zdroje
	- query - key=value forma
	- fragment určuje pozici na stránce (bude načtena u elementu s `id=section2`) - orientace v rámci obsahu (už není součástí úrovně URL, je to doplňková informace pro prohlížeč)
- příklady:
	- `mailto:user@example.org`
	- `ftp://anonymous:user%40example.org@ftp.example.org:21/…`
	- `http://www.example.org:8080/cesta/nazev`
- speciální znaky v URL musí být kódovány pomocí `%` notace
	- buď jsou rezerované nebo nebezpečné (např. jsou speciální při přenosu nebo mohou být při přenosu změněny)
	- např. `%40` je zavináč a tudíž se nechová jako oddělovač
- druhy URI
	- capability URI - krátkodobá URI vytvořená pro jednorázový účel (např. ověření e-mailu)
	- URI Alias - 2 různé URI, které identifikují stejný zdroj
	- URI Collision - 1 URI ukazuje na 2 různé zdroje
	- persistent URI - je validní i když je fyzický zdroj už pryč/smazaný/nerelevantní
		- měl by být nastavený redirect
### URN
= *uniform resource name*
- je trvalý, perzistentní a globálně unikátní identifikátor nějakého zdroje (který není závislý na umístnění zdroje)
	- tedy např. bez `http:`
	- měl by existovat i když už zdroj neexistuje či je zničen
- také je podmnožinou [[#URI]]
- může to být např. ==ISBN knihy== 

![[Pasted image 20250108131321.png]]
(zdroj obrázku: Ing. Lukáš Bařinka, FIT ČVUT, předmět BI-AWD - [HTTP - Apache httpd Web Server Administration](https://lukasbarinka.gitlab.io/apache/http.html#/uri-schema))