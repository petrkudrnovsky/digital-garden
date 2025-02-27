= *HyperText Transfer Protocol*
- sedí na aplikační vrstvě ([[ISO OSI model]]), přenáší soubory mezi klientem a serverem (používá TCP/IP protokol, obvykle port 80/443)
- to to bezstavový protokol
	- funguje na principu požadavek -> odpověď
- šifrovaná varianta: HTTPS (HTTP + SSL/TLS (krytografický protokol pro šifrovanou komunikaci)) - dneska standard, port 443
- existuje celá řada hlaviček, které se používají
	- ale mohu k nim mít i svoje vlastní definované (je to otevřené)
- po hlavičce je prázdný řádek a pak tělo požadavku
### Request
![[Pasted image 20231003113509.png]]
Kdy použít POST místo GET?
- když mám filtrování a překročí to maximální délku URL
- hledání pomocí obrázků
Kdy použít GET místo POST?
- nedává to smysl
GET mění data pouze statistická (počet přístupů apod.)
##### User-Agent
- klient, který inicioval požadavek
- většinou jsou to prohlížeče, editory, roboti apod.
### Response
![[Pasted image 20231003113531.png]]
Proč není dobré vrátit 404-like stránku, ale s kódem 200?
- Protože si při 404 si to prohlížeč nezacachuje a nebude mi to pak nabízet jako oblíbenou stránku
### Stavové kódy
[Stavové kódy](https://cs.wikipedia.org/wiki/Stavov%C3%A9_k%C3%B3dy_HTTP) (200 OK, 404 Not Found...)
- 1xx - informační
	- 100 - Continue - zpracování pokračuje
- 2xx - úspěch
	- 200 - OK
	- 201 - Created
	- 204 - No Content
- 3xx - přesměrování, je nutná další akce pro dokončení
	- 300 - Multiple Choices - více možností např. u dojednávání obsahu
	- 301 - Moved Permanently - URL se perm. změnila, měl bych použít jinou
	- 303 - See Other - zdroj lze najít na jiné URL
	- 304 - Not Modified - zdroj se nezměnil, použij zacachovanou verzi
- 4xx - chyba klienta
	- 400 - Bad Request
	- 401 - Unauthorized (jsi neauthentifikovaný)
	- 403 - Forbidden (jsi nautorizovaný)
	- 404 - Not Found
	- 405 - Method Not Allowed
- 5xx - chyba serveru
	- 500 - Internal Server Error
	- 501 - Not implemented
	- 503 - Service Unavailable
- pokud klient nějakému kódu nerozumí, považuje ho za `x00`, ten základní
### HTTP/1.1
- nová povinná hlavička **Host** (což je doména požadavku)
	- na 1 IP adrese mohlo existovat více domén - ulehčení IPv4
	- umožňuje využívat [[Virtual Hosting]]
- odpověď lze posílat ve více částech (chunked encoding)
	- hlavička `Transfer-Encoding: chunked`, každý blok má na začáku hexadecimálně zapsanou délku toho bloku
	- ukončí se to tak, že se na konci pošle velikost "0"
	- pouze v této verzi protokolu
- podpora trvalého spojení (*Connection: Keep-Alive*)
- pokročilé cacheování
### HTTP/2
- umí posílat CSS, JS a obrázky dopředu bez vyžádání od klienta (rychlejší)
	- upouští se od toho, protože implementace na straně serveru je složitá
- je v binární variantě - tedy pro člověka nečitelná
	- efektivní komprese
### HTTP/3
- umí rychle přecházet mezi např. wi-fi a 5G
	- protože si posílá ID toho spojení
- funguje i nad UDP, má paralelní zpracování
### Nástroje pro komunikaci v protokolu HTTP
- `ping` - zjištění IP adresy, kontrola spojení
- `nslookup` nebo `dig` - informace o doméně z [[DNS]]
- [[nc (Netcat)]]
- [[telnet]]
- `ab` - Apache benchmark - statistiky výkonu webového serveru
- webový prohlížeč - testování uživatelem
	- pozor na cache (nemusí se ihned projevit změny)
### Metody HTTP protokolu
- některé jsou součástí [[REST protokol]]
	- [[REST protokol#GET]]
	- [[REST protokol#POST]]
	- [[REST protokol#PUT]]
	- [[REST protokol#DELETE]]
	- [[REST protokol#PATCH]]
	- [[REST protokol#OPTIONS]]
#### TRACE
- je bezpečná (tj. nezmění zdroj či server)
- slouží k získání informací o změnách provedených na serverech vedoucích k cíli
	- dělá to tak, že pošle požadavek na server a server mu pošle jeho kopii zpátky (a trackuje, jak se cestou ten požadavek změní a přes které servery to šlo)

> [!danger] Rizika
> Mnoho serverů ho má zakázaný, protože může odhalit citlivé údaje
#### CONNECT
- překládá požadavek na spojení na TCP/IP tunel mezi klientem s serverem
- tunel znamená, že proxy jenom přeposílá data mezi koncovými body a nezasahuje do nich
	- jde tak zajistit end-to-end šifrování
- zásadní pro chod HTTPS přes proxy
#### HEAD
- podobné jako [[REST protokol#GET|GET]], ale vrací jenom hlavičky (bez těla odpovědi)
	- díky tomu se šetří data a šířka pásma (můžu rychle zjistit, jestli se soubor změnil (podle `Last-Modified`a případně už soubor nestahovat znovu))
***
### HTTP Autentizace
- viz [[Autentizace]]
- při požadavku přes [[Proxy]] server s autentizací je potřeba přidat hlavičku `Proxy-Authorization`
- formát přihlašovacích údajů je `Basic base64(user:pass)`

> [!warning] Upozornění
> Base64 je jenom kódování (pro ochranu znaků při přenosu), ale není to šifrování (nemá to bezpečnostní hodnotu) 
