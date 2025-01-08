## URI
= *uniform resource identifier*
- identifikuje/pojmenovává zdroje na internetu
- syntaxe je specifická podle zvoleného schématu
### IRI
= *Internationalized Resource Identifier*
- zobecnění [[#URI]], umožňuje používat Unicode místo ASCII
### URL
= *uniform resource locator*
- specifikuje UMÍSTĚNÍ zdrojů na internetu
	- například: lokace souboru/stránky na internetu
- podmnožina [[#URI]]
- jeho schéma: `scheme:[//authority]path[?query][#fragment]` 
	- fragment určuje pozici na stránce (bude načtena u elementu s `id=section2`) - orientace v rámci obsahu (už není součástí úrovně URL)
- příklady:
	- `mailto:user@example.org`
	- `ftp://anonymous:user%40example.org@ftp.example.org:21/…`
	- `http://www.example.org:8080/cesta/nazev`
- speciální znaky v URL musí být kódovány pomocí `%` notace
	- buď jsou rezerované nebo nebezpečné (např. jsou speciální při přenosu nebo mohou být při přenosu změněny)
	- např. `%40` je zavináč a nechová se jako oddělovač
### URN
= *uniform resource name*
- je trvalý, perzistentní identifikátor nějakého zdroje (který není závislý na umístnění zdroje)
	- tedy např. bez `http:`
- také je podmnožinou [[#URI]]
- může to být např. ==ISBN knihy== 

![[Pasted image 20250108131321.png]]
(zdroj obrázku: Ing. Lukáš Bařinka, FIT ČVUT, předmět BI-AWD - [HTTP - Apache httpd Web Server Administration](https://lukasbarinka.gitlab.io/apache/http.html#/uri-schema))