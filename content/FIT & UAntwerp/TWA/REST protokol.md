> [!NOTE] TL;DR
> REST je takový soubor pravidel, jak by měla fungovat komunikace mezi jednotlivými aplikacemi a službami. Každý objekt, který je součástí této komunikace se nazývá *zdroj*. Jednotlivé zdroje mohou mít různé reprezentace a k manipulaci s nimi používáme HTTP metody.
> 
> Díky dodržování REST principů jsme schopni jednoduše škálovat celou komunikaci, rozdělovat aplikaci do oddělených komponent, které spolu komunikují přes definované rozhraní (API) a šetřit bandwidth tím, že efektivně využíváme cache. 
> 
> HTTP metody: GET, POST, PUT, PATCH, DELETE, HEAD, OPTIONS
## Základní koncepty
REST je architektura (nikoliv návrhový vzor) pro implementaci API, kterou vymyslel Roy Fielding. [[HTTP protokol]] je vlastně implementací REST principů.
- zkratka pro REpresentational State Transfer

**Klíčové vlastnosti:**
- Využívá HTTP protokol, ideální pro CRUD operace
- Umožňuje škálování (síťová infrastruktura, servery, software)
- Umožňuje snadné nasazení nezávislých komponent (např. [[Návrh architektury aplikace#Microservices|Microservices]])
- Dneska už by měl být standard implementovat API pro aplikaci, aby nebylo potřeba využívat grafické rozhraní
## Architektonické principy
### Architektura klient-server
- Každá strana má jiné zodpovědnosti
- Middleware - funkcionalita "mezi", která probíhá mezi komunikací
### Vrstvená architektura
- Podporuje vrstvenou architekturu (mezi vrstvami bude middleware)
- Jako uživatel nevnímám cache proxy, loadbalancer, autorizační server
- Je to velmi dobře škálovatelné - autorizační "krabičku" vyměním za jinou
### Bezstavovost (Statelessness)
- Požadavek obsahuje všechny informace (autorizaci, token, ID zdroje apod.)
- Umožňuje jednoduše škálovat mezi servery
- U [[Session]] (která řeší stavy webových aplikací) se to řeší většinou s:
	- Centrální databází uložených sessions
	- Bezstavové tokeny (JWT apod.), které všechny informace obsahují v sobě
### Uniformní rozhraní
Konečný počet operací - požadavek na REST.

**Založené na zdrojích:**
- Zdroj může být cokoliv (fyzická osoba, entita)
- Zdroje mají svoje identifikátory ([[URL, URI, URN, IRI]])
- Zdroje mají reprezentaci (HTML, JSON...) a svoje metadata (media type (IANA), čas poslední úpravy, zdrojový odkaz)
	- Jeden zdroj může mít více různých reprezentací (jak textové, tak binární)
		- Klienti si o ně říkají např. pomocí `Accept` hlaviček
		- Měly by být v rámci IANA
	- O reprezentaci se informuje hlavičkou `Accept` (klient, "co chci") a `Content-Type` (server, "co posílám")
- Co se s tím zdrojem bude dít, je dáno REST metodou
- **Názvy zdrojů by neměly implikovat způsob použití** - ne `/getOrders`, ale spíš `GET /orders`

**Stavy zdrojů:**
- Zdroje mohou mít různé stavy, a každý stav může mít více reprezentací
- Např. zdroj `/orders` má stav s 2 objednávkami, pak se stav změní na 3 objednávky apod.
- Unsafe REST metody vlastně mění stav zdroje
- Změna stavu zdroje vlastně změní i stav aplikace

**Metadata zdrojů:**
- Mohou být definovány [[04 Svět znalostí/IT related/Webařina/Další témata/HTTP protokol|HTTP]] hlavičkou nebo být součástí datového formátu (např. meta tagy v HTML)
### HATEOAS (Hypertext As The Engine Of Application State)
- [HATEOAS](https://cs.wikipedia.org/wiki/HATEOAS) na Wikipedii, jedná se o klíčový princip RESTu
- Všechny následující a možné akce vztahující se k vrácenému zdroji jsou obsažené v odpovědi vrácené serverem
	- Tedy se vždy posílá "stav zdroje" (vyjádřený pomocí HTTP metadat a možných odkazů na přidružené zdroje a navazující REST operace) a není potřeba [[Session]] pro informace o stavu zdroje (ale samozřejmě to má svoje limity)
	- Ale HATEOAS se velmi dobře škáluje (narozdíl od Session) - pořád je 100 % stateless
- Pomocí odkazů se dají také získávat relevantní data, metadata či akce k odpovědi
- Moje myšlenka:
	- Stejně jako odkazy velmi usnadňují navigaci na webových stránkách (vidím odkazy na relevantní zdroje, na košík, na platbu apod.)
	- Tak stejně funguje HATEOAS
##### Atom Syndication format
- Jeden ze způsobů, jak reprezentovat odkazy, které v rámci HATEOAS chodí s response
- Jedná se o standardizovaný formát (původně využívaný na RSS), založený na [[FIT & UAntwerp/MDF/XML]]
- Je standardizovaný, ale už se tolik nepoužívá (spíš HAL a JSON-LD se teď používají)
	- `rel` - název odkazu, který většinou sémanticky ukazuje smysl/operaci daného linku
		- např. pro navigaci může mít `rel` hodnoty: next, previous, self
		- nebo může mít název: addItem, deleteOrder atd.
	- `href` - URI na zdroj, na který link ukazuje
	- `type` - jaký je media type zdroje, na který link ukazuje
- Alternativa je uvádět odkazy přímo v hlavičce response (mají stejnou sémantiku jako Atom, ale není kvůli nim potřeba načítat celý zdroj, ale jenom hlavičku (pomocí HEAD metody))
##### HAL (Hypertext Application Language)
- Společně s response posílá `_links` - odkazy na další zdroje
	- Může sám na sebe ("self"), nebo na edit, remove apod.
	- Vždy by to měly být validní zdroje v rámci práv (pro konkrétního uživatele)
- Nebo posílá `_embedded` - navazující/vnořené entity (např. další položka u stránkování)
- **V praxi:** Tyto principy jsou fajn na to být fakt RESTful, ale v praxi se používá spíše jednoduchý JSON a dokumentace ve stylu Swagger/OpenAPI
### Cacheovatelnost
V rámci RESTu se pracuje primárně s daty, to se dá snadno cachovat. Správné je vždy cachovat statické zdroje. Dynamické zdroje se dají cachovat také (a implementovat mechanismy jako timestamps, ETags apod.)

**Cache-Control direktivy:**
- uvádí informace pro [[Proxy]], přes které požadavek prochází
	- `private` - žádná proxy nesmí cachovat, pouze klient
	- `public` - každý si ji může zacachovat i routery po cestě
	- `no-cache` - nesmí se cachovat, pokud se zacachuje, musí se vždy revalidovat
- `max-age=<seconds>` - životnost cache
- Lze ji "vynutit" tím, že na serveru nastavím reject limit, aby mi klienti nevytěžovali zbytečně prostředky
	- Je důležité mít rozumně nastavené age-limit
	- A tím pádem si budou klienti více cachovat, aby se nemuseli tolik dotazovat serveru (samozřejmě po čase je to nutné a k tomu právě slouží ten age-limit)

**Revalidace:**
- Má smysl cachovat i na 0 sekund (tím říkám, že pokaždé je potřeba revalidovat zdroj u serveru)
- Když se znovu dotážu (`If-Modified-Since`) a pokud se obsah nezměnil, pošle se 304 (nic se nezměnilo = využij to, co máš zacachované)
	- pro ETagy se používá `If-None-Match`
	- tomuto se říká *Conditional GET* 
		- chci zdroj jenom pokud se změnil - pokud se nezměnil od současné verze, kterou mám jako klient u sebe, tak použiju svoji zacachovanou verzi (je to rychlejší + šetřím zdroje serveru)
- `max-age=0` a `must-revalidate` je to stejné

**ETag (novější než Last-Modified):**
- Může to být ID nebo hash
- Užitečné, když se mění data opravdu rychle - LastModifiedTime nerozliší svojí přesností, jestli se něco změnilo
- **Strong Etag** - jakmile se změní 1 bit obsahu, ihned je jiný (je to hash)
- **Weak ETag** - dojde ke změně, ale ta není tak důležitá pro znovu-generování stránky
	- definuje ho samotná aplikační logika, která si to řeší sama
	- např. se hodí pro složené objekty (např. změna pořadí není potřeba stahovat nový zdroj), ale pokud se přidá např. nový článek, tak už se Weak ETag změní a bude se revalidovat
- pokud server pošle oboje (Last-Modified-Since a ETag), což by ideálně měl, tak Etag má preferenci

**Proxy cache:**
- První uživatel dostane celou stránku a další dostanou už tu zacachovanou (používá se například ve firmách kdy více uživatelů opakovaně přistupuje na ty stejné stránky)
### Souběžnost (Concurrency)
- Nastává v momentě, kdy 2 klienti aktualizují jeden zdroj ve stejnou chvíli (či jeden aktualizuje a druhý čte apod.)
	- řeší se pomocí *Conditional PUT*, kdy se aktualizuje pouze pokud se zdroj nezměnil od určitého data či jestli sedí ETagy 
		- `If-Unmodified-Since` a `If-Match` hlavičky
	- pokud ne, tak je to 412 Precondition Failed
		- protože je zdroj novější než ho mám u sebe a je riziko toho, že budu přepisovat (a ani nevím co)
## HTTP metody
### Vlastnosti metod
- **Safe (bezpečné)** - můžeme zacachovat, nemění stav zdroje (pouze na čtení)
- **Idempotentní** - opakované volání metody má pořád stejný efekt (zdroj zůstává ve stejném stavu)
	- Výsledek může být jiný, jde o ten efekt, nemá měnit stav aplikace
### GET
- Požadavek na reprezentaci aktuálního stavu zdroje (reprezentován URI)
- **Idempotentní i safe**
- Možnost partial GET (když se stahování přeruší nebo stahuji z více zdrojů najednou)
- Dá se dobře cachovat - ptám se serveru, aby mi poslal soubor jenom pokud není mladší než X
### POST
- Odeslání informací v těle požadavku na server ke zpracování
- Používá se k vytvoření nového prvku v kolekci
	- Server novému zdroji vygeneruje nové ID (klient pouze dodá obsah a URL)
- **Není safe a není idempotentní**
### PUT
- Používá se pro plnou **náhradu** či vytvoření celého zdroje
- Pokud ID neexistuje, tak se automaticky vytvoří
- **Idempotentní**
### DELETE
- Smazání zdroje
- **Idempotentní** - 2x smažu a výsledek je pořád stejný (je to smazané)
### PATCH
- Používá se pro částečnou změnu zdroje
- **Není idempotentní ani safe**
- Příklad: změna množství o 50ml - opakování akce vždy zvýší množství o 50ml (PUT by to nastavil vždy na stejnou hodnotu)
- Formát requestu může být v podstatě jakýkoliv - záleží na konkrétní implementaci (bude v dokumentaci)
### HEAD
- Stejné jako GET, ale pouze pro hlavičky
- **Safe**
- Např. pro zjištění ETagu u velkého zdroje - stahovat chci pouze pokud existuje nová verze (šetřím bandwidth)
### OPTIONS
- Požadavek na informace o možnostech zdroje (URI)
- Zjištění, jaké HTTP metody mohu používat
- **Safe**
- Dá se použít jako `ping`
## REST antipatterns
Spoustu firem a programátorů v praxi porušují pravidla REST -> vytvářejí se antipatterny.

**Příklady:**
- `GET /orders/?add=new_order` - používání metod k jinému účelu než mají sloužit
	- Rozbíjí to idempotenci a safe/unsafe
	- GET požadavky se běžně cachují (např. pomocí proxies) a pak se zacachuje přidání nového zdroje (to není žádoucí chování)

**Poznámka:** Existují už hodně zavedené antipatterny, které se rozšířeně používají. Je důležité vědět, proč a jak by se to mělo dělat správně.