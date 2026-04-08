## Základní koncepty
HTTP (*HyperText Transfer Protocol*) je bezstavový protokol sedící na aplikační vrstvě ([[ISO OSI model]]), který přenáší soubory mezi klientem a serverem.
- Používá [[TCP IP model|TCP/IP]], obvykle port 80 (HTTP) nebo 443 (HTTPS)
- Funguje na principu požadavek → odpověď
- Je bezstavový
### HTTPS (HTTP Secure)
- HTTP + SSL/TLS (kryptografický protokol pro šifrovanou komunikaci)
- Dneska standard, port 443
- Viz [[Bezpečnost webového serveru]]
### Struktura HTTP zpráv
- Existuje celá řada hlaviček, které se používají
- Můžu mít i svoje vlastní definované hlavičky (je to otevřené, jenom musím na aplikační vrstvě zařídit, aby jednotlivé aplikace hlavičkám rozuměly)
- Po hlavičce je prázdný řádek a pak tělo požadavku
##### HTTP Request
```
Request     ::= RequestLine (Header CRLF)* CRLF [body]
RequestLine ::= Method RequestUri HTTPVersion CRLF
Header      ::= Host | Accept | ...
Method      ::= GET | HEAD | ...
```
Požadavek reprezentuje akci uživatele, přičemž:
- metoda určuje, o jakou "akci" se jedná
- URL adresa určuje "cíl" dané akce
### Příklad požadavku
```
GET /profile/140f269cb240 HTTP/1.1
Host: usermap.cvut.cz
Accept: text/html
```
##### HTTP Response
Odpověď se skládá ze stavové řádky, následované nula nebo více hlavičkami (každá zakončená CRLF), prázdnou řádkou (CRLF) a volitelným tělem:

```
Response   ::= StatusLine (Header CRLF)* CRLF [body]
StatusLine ::= HTTPVersion Status CRLF
Status     ::= StatusCode ReasonPhrase
Header     ::= Content-Type | ...
```
### Ukázka konkrétní odpovědi
Příklad skutečné HTTP odpovědi vypadá takto - nejprve stavová řádka s verzí protokolu a kódem 200 OK, poté hlavičky s datem, délkou obsahu a typem obsahu, a nakonec samotné HTML tělo:

```
HTTP/1.1 200 OK
Date: Sun, 5 Jul 2020 16:46:06 GMT
Content-Length: 39
Content-Type: text/html

<html>
    <body>Ahoj svete!</body>
</html>
```
##### Otázky
- Kdy použít POST místo GET?
	- Když mám filtrování a překročí to maximální délku [[URL, URI, URN, IRI|URL]]
	- Hledání pomocí obrázků (kdy se jako query posílá obrázek)
- Kdy použít GET místo POST?
	- Nedává to smysl (GET by se měl používat pouze pro čtení dat)
	- GET mění pouze statistická data (počet přístupů, datum přístupu apod.)
	- `GET /orders/?add=new_order` je špatně
		- nerespektuje to sémantiku REST a vytváří nekonzistenci
		- proxy na cestě si to může zacachovat a po čase samo od sebe revalidovat - tímto příkazem se při revalidaci přidá další zdroj (což většinou nechceme)
- Proč není dobré vrátit 404-like stránku s kódem 200?
	- Prohlížeč si při standardním kódu 404 nezacachuje stránku a nebude ji nabízet jako oblíbenou
		- Pokud pošlu kód 200, tak ji prohlížeč bude vnímat jako klasickou stránku a zacachuje si ji
	- Porušuje to sémantiku HTTP protokolu
		- Vývojáři v mém týmu či po mně tomu nebudou rozumět
### User-Agent
- Klient, který inicioval požadavek
- Většinou jsou to prohlížeče, editory, roboti apod.
## Stavové kódy
- [Stavové kódy na Wikipedii](https://cs.wikipedia.org/wiki/Stavov%C3%A9_k%C3%B3dy_HTTP) (200 OK, 404 Not Found...)
##### 1xx - Informační
- **100 Continue** - zpracování pokračuje
##### 2xx - Úspěch
- **200 OK** - požadavek byl úspěšný
- **201 Created** - nový zdroj byl vytvořen
- **204 No Content** - úspěch, ale žádný obsah k vrácení
##### 3xx - Přesměrování
- Je nutná další akce pro dokončení požadavku.
- **300 Multiple Choices** - více možností, např. u dojednávání obsahu
- **301 Moved Permanently** - URL se permanentně změnila, měl bych použít jinou
- **303 See Other** - zdroj lze najít na jiné URL
- **304 Not Modified** - zdroj se nezměnil, použij zacachovanou verzi
##### 4xx - Chyba klienta
- **400 Bad Request** - špatně formulovaný požadavek
- **401 Unauthorized** - jsi neautentizovaný - [[Autentizace]]
- **403 Forbidden** - jsi neautorizovaný (nemáš práva) - [[Autorizace]]
- **404 Not Found** - zdroj nebyl nalezen
- **405 Method Not Allowed** - HTTP metoda není pro tento zdroj povolena
- **406 Not Acceptable** - server nemůže serializovat zdroj do požadovaného formátu
- **412 Precondition Failed** - záleží na implementaci na serveru (někdy to vrátí, zkombinuje to na základě cache, co se upravovalo atd.)
##### 5xx - Chyba serveru
- **500 Internal Server Error** - obecná chyba serveru
- **501 Not Implemented** - server neimplementuje požadovanou funkcionalitu
- **503 Service Unavailable** - server je dočasně nedostupný

**Poznámka:** Pokud klient nějakému kódu nerozumí, považuje ho za `x00` (základní kód dané kategorie).
## HTTP verze

#### HTTP/1.1
**Nové vlastnosti (oproti HTTP/1.0):**
- Povinná hlavička **Host** (doména požadavku)
	- Na 1 IP adrese mohlo existovat více domén - ulehčení problémů nedostatku IPv4 adres
	- Umožňuje využívat [[Virtual Hosting]]
- Odpověď lze posílat ve více částech (**chunked encoding**)
	- Hlavička `Transfer-Encoding: chunked`
	- Každý blok má na začátek hexadecimálně zapsanou délku
	- Ukončí se velikostí "0"
	- Pouze v této verzi protokolu
- Podpora trvalého spojení (`Connection: Keep-Alive`)
- Pokročilé cacheování
### HTTP/2
Vytvořena primárně pro zrychlení komunikace a vychytání chyb v HTTP/1.x. Hlavně modifikuje to, jak jsou data reprezentována a přenášena v komunikaci.

**Problémy HTTP/1.x:**
- Pro dosažení paralelismu musel klient vytvořit více spojení najednou
- Hlavičky nebyly komprimované
- Žádná prioritizace zdrojů (všechny měly stejnou váhu a prioritu)
- To vedlo k zbytečnému síťovému provozu

**Výhody HTTP/2:**
- Umí posílat relevantní CSS, JS a obrázky dopředu bez vyžádání od klienta (rychlejší)
	- Jmenuje se to "Server Push"
	- Na jeden request může server poslat více responses
	- Ty "dobrovolné" navíc označí pomocí PUSH_PROMISE, aby klient věděl, že budou poslané a nevytvářel duplicitní požadavky
	- Posílá se jenom, pokud je "idle time", jinak to nemusí být vždy efektivní
- Umí multiplexovat a posílat požadavky paralelně (není potřeba čekat)
	- Umí prioritizovat jednotlivé streams
		- každý stream má určitou váhu a podle toho je prioritizován na prostředcích
	- Umí prioritizovat jednotlivé assety podle typu, lokace atd.
	- Umí se "učit" - pokud nějaký asset blokuje stránku (např. JS), zvýší mu prioritu (a tím bude poslaný dříve a bude mít více prostředků a je menší pravděpodobnost, že bude zase dlouho blokovat)
- Efektivní komprese HTTP hlaviček pomocí Huffmanova kódu
- Umí flow control (podobně jako u TCP), regulace provozu podle zaneprázdnění příjemce

**Nevýhody HTTP/2:**
- Závislost na [[TCP protokol]]u (TCP handshakes + TLS handshakes trvají dlouho)
- TCP vždy znovuposílá ztracené segmenty - to zpomaluje celý přenos
- Komplexní implementace na straně serveru
	- Např. klouzavé okénko pro regulaci flow streamu

**Implementační detaily:**
- Je v **binární variantě** (nečitelná pro člověka, ale to neznamená, že samotné binární kódování zajišťuje bezpečnost)
- Efektivní komprese
- Sestaví se TCP Stream pro multiplexování frames (HEADERS frame, DATA frame)
	- tj. hlavička a data z HTTP/1.1 jsou posílané v jednotlivých HEADERS a DATA frames
	- stream umožňuje obousměrný provoz
- Jednotlivé frames jsou součástí Messages (1 response/request = 1 Message)
	- Message je sekvence jednotlivých frames

**Upgrade na HTTP/2:**
- Během HTTP/1.1 komunikace lze poslat hlavičku `Upgrade: h2c` společně s `HTTP2-Settings`
- Pokud příjemce podporuje HTTP/2, přehodí se na lepší verzi
- Fallback pro HTTP/2 je HTTP/1.1
- Při inicializaci je požadavek na HTTP/2.0 součástí ClientHello zprávy
### HTTP/3
**Klíčové vlastnosti:**
- Umí rychle přecházet mezi sítěmi (např. Wi-Fi a 5G)
	- Posílá si ID spojení mezi klientem a serverem (nezávislé na zdroji)
	- U TCP docházelo k Connection timeout při změně sítě a bylo potřeba obnovit spojení
- Běží nad protokolem **QUIC** (který běží přes [[UDP protokol]])
	- [[TCP protokol]] blokování při ztrátě packetu bylo limitující
	- QUIC implementuje packet loss detection, ale blokuje se pouze jedno vlákno (ne celý přenos) - tj. na úrovni jednoho streamu
	- QUIC handshake je rychlejší než HTTP/2 (TCP + TLS handshake)
		- Oproti 3xRTT (SYN - SYN ACK - ACK + ClientHello - ServerHello + Cert - šifrovací klíč + FIN - šifrovací klíč + FIN - Data) je jenom 1xRTT (Init + Hello - Init + Hello + Cert - FIN + Data)

## HTTP metody
- Metody z REST protokolu
	- [[REST protokol#GET|GET]]
	- [[REST protokol#POST|POST]]
	- [[REST protokol#PUT|PUT]]
	- [[REST protokol#DELETE|DELETE]]
	- [[REST protokol#PATCH|PATCH]]
	- [[REST protokol#OPTIONS|OPTIONS]]
	- [[REST protokol#HEAD|HEAD]]
#### TRACE
- **Safe metoda** (nezmění zdroj či server)
- Slouží k získání informací o změnách provedených na serverech vedoucích k cíli
- Klient pošle požadavek a server vrátí jeho kopii zpátky
- Trackuje, jak se cestou požadavek změní a přes které servery prošel

> [!danger] Rizika
> Mnoho serverů ho má zakázaný, protože může odhalit citlivé údaje
### CONNECT
- Požádá [[Proxy]] o vytvoření spojení (tunelu) mezi klientem a serverem
- Pokud se úspěšně vytvoří, proxy pak pouze slepě přeposílá data bez zásahů
- **Tunel** = proxy pouze přeposílá data mezi koncovými body, nezasahuje do nich
	- Zajišťuje end-to-end šifrování
- CONNECT je zásadní pro chod HTTPS přes [[Proxy]]
## HTTP autentizace
- Viz [[Autentizace]] či [[Autentizace a autorizace v rámci Apache httpd]]
- Při požadavku přes [[Proxy]] server s autentizací je potřeba přidat hlavičku `Proxy-Authorization`
- Formát přihlašovacích údajů: `Basic base64(user:pass)`

> [!warning] Upozornění
> Base64 je pouze kódování (pro ochranu znaků při přenosu), není to šifrování a nemá bezpečnostní hodnotu
## Nástroje pro komunikaci v HTTP protokolu
- **ping** - zjištění IP adresy, kontrola spojení
- **nslookup** nebo **dig** - informace o doméně z [[DNS]]
- [[nc (Netcat)]]
- [[telnet]]
- **ab** (Apache Benchmark) - statistiky výkonu webového serveru
- **Webový prohlížeč** - testování uživatelem
	- Pozor na cache (změny se nemusí ihned projevit