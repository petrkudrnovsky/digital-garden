2 způsoby handlování modulů
- 1) zkompilované do binárky (statické moduly) - je to 1 soubor, jednoduché handlování, ale musím při každé změně v modulu rekompilovat celý server
	- a zároveň pak když mám více obslužných procesů, tak ho každý musí mít v sobě
- 2) `mod_so` (dynamické moduly) - mám to zkompilované stranou, když to server potřebuje, tak si to natáhne (dynamicky)
	- `mod_so` je jeden z mála staticky kompilovaných modulů (aby server uměl natahovat další moduly)
- kontrola existence modulu přes [[Direktivy pro Apache httpd|direktivu]] `<IfModule>`
### mod_so - dynamické načítání knihoven
- `LoadModule [id modulu] [umisteni souboru]`
	- umístění je relativně k `ServerRoot`
- běžná notace názvu dynamické knihovny: `[název modulu]_module`
	- k tomu umístění `modules/mod_[název modulu].so`
- staticky nalinkované moduly mohu ihned používat
### mod_dir
- direktiva `DirectoryIndex`
	- když je požadavek na adresář, tak server pošle soubor z tohoto adresáře, který je označený touto direktivou
### mod_autoindex
- u `mod_dir`, když systém nenajde hledaný soubor, tak jeden automaticky vytvoří
	- většinou zobrazí jednoduchý obsah adresáře (to se pak dá upravovat)
### mod_alias
- direktiva `Alias`
	- říká "tenhle prefix na filesystému je tenhle prefix v adrese"
		- v poslané adrese se to nahradí a pak to tvoří cestu k nějakému souboru na filesystému
	- `Alias /foo /var/share/xyz`

`Options +Indexes`
- to + znamená, že k těm zděděným Options přidám další a nepřepíšu je
## [[Moduly pro souběžné zpracování požadavků]]
## [[Moduly pro práci s MIME informacemi]]
## Moduly pro logování
### mod_status
- kontrola výkonu serveru, informace o běhu serveru (prostě *status* serveru)
- dostanu [[HTML]] stránku se statusem (doba běhu, využití CPU, počet vláken a jejich statusy apod.)
### mod_info
- informace o nastavení serveru (použité moduly a informace k nim)
- je dobré k `/server-info` omezit přístup, informace mohou být citlivé
### mod_log_config
- custom nastavení logů
- základní logování už je v jádru Apache httpd 
- mohu si naformátovat svůj vlastní `CustomLog`