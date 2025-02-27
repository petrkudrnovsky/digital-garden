Direktivy jsou pravidla, kterými můžu definovat jak se má server chovat, co je a co není dovoleno, kam se kdo může dostat apod. Direktivy píšu do konfiguračních souborů (`apache.conf`, `httpd.conf`)

> Index direktiv a jejich podrobná dokumentace: [Directive Index - Apache HTTP Server Version 2.4](https://httpd.apache.org/docs/current/mod/directives.html)

`ServerRoot`
- kořenový adresář s instalací serveru, určuje adresář, kde jsou všechna globální nastavení webserveru, konfigurace apod.
- když někde zadám relativní cestu, tak se bere vůči nastavenému `<ServerRoot>`

`Listen`
- na jakém portu se poslouchá (IP adresa/jméno, TCP port)
- můžu mít více IP adres, tak je mohu mít na jiných portech

`ServerName`
- doména, jméno serveru pro odpovědi (např. při redirectu)
- když se třeba přesměrovává na relativní adresu (tak se tam přidá ještě tenhle server name, aby to bylo kompletní absolutní adresa)
- hodí se také u [[Virtual Hosting]], kde slouží k rozlišování jednotlivých VirtualHosts podle jména

`DocumentRoot`
- kořenová složka pro soubory, které jsou pak dostupné veřejně na webu (mapování kořene webu na filesystém)
- udává, jaký adresář na serveru koresponduje s "/"
- udává, kde má Apache hledat soubory a zdroje požadované klientem
- díky tomu také funguje ochrana, že se nedá jít "výš" dotazem na "../../../" atd.
`AddDefaultCharset` - nastavení znakové sady
`Define` - definuje proměnnou, která se pak v konfiguraci může použít na více místech
- viz [[Konfigurace Apache httpd#Proměnné v konfiguraci]]
- každý [[Virtual Hosting|Virtual Host]] může být jiný document root

Veškerou obsluhu chci vést pod jiným uživatelem (který není root)
- root je jenom na načtení configu, otevření portu atd.
- direktivy `User` a `Group` pro specifikování identity uživatele a skupiny

Další direktivy najdu v poznámce [[Moduly pro Apache httpd]]
### Logování
- `ErrorLog`, `LogLevel`
- `LogFormat`, `CustomLog` - vlastní logování (v rámci [[Moduly pro Apache httpd|modulu]] mod_log_config)
### Řízení přístupu
- direktiva `Require` - říkají, za jakých podmínek budou zdroje přístupné
	- definují se v rámci kontejnerových direktiv ([[#Kontejnery pro direktivy]])
	- můžu požadovat uživatele nebo specifickou adresu
- `RequireAny` musí se splnit alespoň jedna podmínka
- `RequireAll` musí se splnit všechny podmínky
## Kontejnery pro direktivy
- pokud je direktiva mimo kontejner, tak platí pro celý server
	- některé direktivy musí být v nějakém kontejneru, jinak nedávají smysl
- kontejnery:
	- podle částí souborového systému
		- `<Directory>, <DirectoryMatch>, <Files>, <FilesMatch>`
	- podle zdroje ([[URL, URI, URN, IRI#URL|URL]])
		- `<Location>, <LocationMatch>, <Proxy>`
	- podle website
		- `<VirtualHost>` viz [[Virtual Hosting]]
	- podmínkové
		- `<IfDefine>`, `<IfModule>` - vyhodnocují se při startu serveru
		- `<If podmínka> .. </If> <ElseIf> <Else>` - vyhodnocují se vždy
			- nemohu je do sebe vnořovat

> [!danger] Upozornění
> Kontejner `<Directory />` je povolen defaultně pro všechny, to znamená, že Apache defaultně zpřístupní vše, o co se dá říct [[URL, URI, URN, IRI#URL|URL]] - obvykle je nutné přístup do `/` zakázat a pak povolovat jenom určité části souborového systému (`Require all denied`) . Jinak je to bezpečnostní riziko.







