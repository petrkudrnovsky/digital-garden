Zkratka PWA
- řeší vývoj aplikací pro různé platformy
- aplikace dodávaná přes web
	- možnost ji nainstalovat do počítače
	- z hlediska systému se tváří jako nativní aplikace (může využívat HW zdroje)
	- browser je na pozadí, takže to běží v prohlížeči, ale není vidět
- ikonka je potřeba (aby se mohla zobrazovat na ploše pc, telefonu apod.)
### PWA Manifest
- JSON soubor, který definuje níže uvedené věci, které jsou potřeba pro "instalaci" aplikace
- definuje:
	- název aplikace
	- link na ikonku aplikace
	- preferovaná URL pro start aplikace
	- konfigurační data
	- display mode (např. full-screen)
### Service workers
- skripty, které běží na pozadí v prohlížeči a umožňují funkcionalitu PWA offline
- mohou mít přístup k prohlížeči i když aplikace není zapnutá
- cachování fetch() - uživatel bez internetu nic nepozná dokud nepřejde na stránku, která není zacachovaná
- nemají přístup k DOMu
- umí notifikace do systému
	- proto běží i když aplikace není zapnutá
	- to jsou ty upozornění, jeslti mi stránka může posílat push-notifikace
- využívají se také na bitcoin-mining
	- když přistoupím na nějaký malicious web
### Data Storage
- WebStorage
	- je perzistentní, klíč=hodnota
	- větší prostor než v Cookies
- proč ukládat něco na straně klienta?
	- zase při práci offline - se moje změny (které jsou uložené ve frontě změn) se pak aplikují až se připojím na internet
- sdílení dat mezi více okny prohlížeče

Indexed Database API - nahradilo deprecated WebSQL
- velikost je daná prohlížečem (teoreticky neomezená)
- je to NoSQL databáze, ukládá JSON objekty
- cachování, offline perzistence
### WebSockets
- trvalé spojení s klientem (statefull), hlavičky se posílají jen jednou
- nutné implementovat nějaký svůj komunikační/aplikační protokol
- pro zpracování vyžaduje speciální server
- server 0.0.0.0 - je otevřený pro všechny IP adresy
- efektivnější než HTTP Polling - kdy se klient v intervalech ptá serveru na nové informace
- využití
	- hry, chaty, burzovní aplikace, monitoring, streaming
### Další API
- PWA aplikace využívají API, aby mohly být plnohodnotnými aplikacemi
- https://developer.mozilla.org/en-US/docs/Web/API
- Push API, Notification API
- Geolocation API
- Barcode Detection API
- Bluetooth API
- Baterry API atd.