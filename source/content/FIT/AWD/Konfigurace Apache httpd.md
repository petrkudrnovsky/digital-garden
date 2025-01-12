- dokumentace ke konfiguraci je součástí konfiguračního souboru
	- většinou je to odstavec zakomentovaného textu nad direktivou, který ji vysvětluje a popisuje možnosti použití
- po změně konfigurace musím restartovat server
	- to je nepříjemné, pokud server běží a obsluhuje klienty
	- chci tak část nastavení delegovat na uživatele -> [[htaccess soubor]]
- konfigurace je často generovaná ze šablon (pomocí skriptů)
## Různé přístupy ke konfiguraci
- obecně jsou 2 přístupy ke konfiguraci
	- celá konfigurace dohromady v jednom souboru (např. Alpine Linux)
	- ve více souborech, např. 1 modul = 1 konfigurační soubor (např. Debian)
		- pro propojení se používá `include`
![[Pasted image 20250111162826.png|600]]
Struktura konfigurace přímo pro Debian:
![[Pasted image 20250111162927.png|600]]
## Syntaxe
- jedna direktiva na jeden řádek (pokud rozděluji na více řádků, tak `\`)
- direktivy jsou case insensitive 
- komentáře uvedu znakem `#`
- prázdné řádky se ignorují (stejně jako bílé znaky před direktivami)
## Proměnné v konfiguraci
- mohu je definovat v:
	- [[Direktivy pro Apache httpd|direktivě]] `Define` - to má prioritu
	- v proměnných prostředí - `/etc/apache2/envvars`
	- při spuštění z příkazové řádky - `apache2ctl -k restart -D ..=..`
		- `-D` jako define
- pak je použiju jako `${APACHE_MANUAL}` v samotné konfiguraci
- `HTTP_*` - parametry [[HTTP protokol]]
- `REQUEST_*` - informace o požadavku
- `REMOTE_*` - informace o klientovi
- `SERVER_*` - informace o serveru