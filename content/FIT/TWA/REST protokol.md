Full name: *representational state transfer*

Dneska už by měl být standard implementovat API pro aplikaci, aby nebylo potřeba využívat grafické rozhraní.

- využívá [[HTTP protokol]] a je to ideální pro CRUD operace
- není to návrhový vzor, je to architektura
- vymyslel to pan Fielding s tím, že REST udává to, jak má komunikace na internetu vypadat
	- HTTP je vlastně implementací RESTu
- umožňuje nám škálovat (síťovou infrastrukturu, počet serverů, softwarová škálovatelnost atd.)
	- např. ta bezstavovost umožňuje jednoduše škálovat mezi servery
- umožňuje snadné nasazení nezávislých komponent (např. [[Návrh architektury aplikace#Microservices|Microservices]])
- middleware - funkcionalita "mezi", která probíhá mezi komunikací

- funguje to jako architektura klient-server (každá strana má jiné zodpovědnosti)
- podporuje vrstvenou architekturu (mezi vrstvami bude middleware)
	- tím pádem jako uživatel nevnímám cache proxy, loadbalancer, autorizační server
	- je to velmi dobře škálovatelné - autorizační "krabičku" vyměním za jinou
- uniformní rozhraní - požadavek na REST
	- **založené na zdrojích** (jako zdroj může být cokoliv - fyzická osoba, entita)
		- mají IDs: [[Webové technologie#Základy a pojmy|URL, URN]]
		- reprezentaci (HTML, JSON...) a jejich metadata (media type, čas poslední úpravy)
		- metadata zdroje (zdrojový odkaz...)
		- co se s tím zdrojem bude dít, to je dáno [[#Metody|REST metodou]]
		- [HATEOAS](https://cs.wikipedia.org/wiki/HATEOAS)
			- všechny následující a možné akce jsou obsažené v odpovědi vrácené serverem
			- takhle se pomocí odkazů dají také získávat relevantní data či akce k odpovědi
- cacheovatelnost - hlavně se získávají data, to se dá snadno cachovat
	- Cache-Control: public (každý si ji může zacachovat i routery po cestě)
	- lze to i "vynutit" tím, že na serveru nastavím reject limit, aby mi klienti nevytěžovalli zbytečně prostředky
		- je ale důležité mít rozumně nastavené age-limit
		- max-age=\<seconds\> - životnost cache
	- má smysl cachovat i na 0 sekund
		- když se znovu dotážu znovu (If-Modified-Since) a pokud se ten obsah nezměnil, pošle se 304 (nic se nezměnilo = využij to, co máš zacachované)
	- ETag - novější (může to být ID nebo hash)
	- protože když se mění data opravdu rychle, tak mi LastModifiedTime nerozliší svojí přesností, jestli se něco změnilo nebo ne
	- Weak ETag - dojde ke změně, ale ta není tak důležitá pro generování stránky
- proxy také používá cache
	- takže první uživatel dostane celou stránku a další dostanou už tu zacachovanou
- max-age = 0 a must-revalidate je to stejné
- bezstavovost
	- požadavek obsahuje všechny informace (autorizaci, token, ID zdroje apod.)

Code on Demand
- v praxi se moc nevyužívá - není potřeba vědět

Identifikace zdroje:
- je důležité si zvolit jeden z konceptů hierarchického rozložení a používat ho
### [HAL](https://en.wikipedia.org/wiki/Hypertext_Application_Language)
Eliminace logiky v [[URL, URI, URN, IRI#URL|URL]] (což je v RESTu identifikátor)
- posílání více informací ke zdroji
- jsou tam informace k tomu konkrétnímu dokumentu
	- a v \_embedded jsou navazující entity (rozklik, další ve stránkování atd.)
## Metody
U konkrétní instance:
- bezpečné (safe) můžeme zacachovat - nemění stav zdroje (pouze na čtení)
- idempotentní - opakované volání metody má pořád stejný efekt
	- výsledek může být jiný, jde o ten efekt, nemá měnit stav aplikaci
### GET
- požadavek na zdroj (reprezentován [[URL, URI, URN, IRI#URI|URI]])
- idempotentní i safe
- můžu mít i partial GET 
	- když se mi stahování přeruší nebo stahuji z více zdrojů najednou
- dá se dobře zakešovat - ptám se serveru, aby mi poslal soubor jenom pokud není mladší než X
	- pokud není nová verze, tak cache pošle zakešovaný soubor
### POST
- odeslání informací v těle požadavku na server ke zpracování
- nepoužívá se u konkrétní instance (k vytvoření se použije [[#PUT]])
	- není safe a není idempotentní
	- používá se k vytvoření nového prvku v kolekci
### PUT
- je tam celý zdroj, používá se pro plnou **náhradu** celého objektu
	- pokud ID neexistuje, tak se automaticky vytvoří
- také je idempotentní
### DELETE
- idempotentní 
	- 2x smažu a výsledek je pořád stejný - je to smazané
### PATCH
- není idempotentní ani safe
	- používá se pro změnu! Například změna množství o 50ml - tedy, když budu opakovat akci vícekrát, tak se vždy zvýší množství o 50ml - PUTem by se to nastavilo vždy na tu stejnou hodnotu
	- formát [[HTTP protokol#Request|Requestu]] může být v podstatě jakkýkoliv - pak záleží na konkrétní implementaci - a to kdyžtak bude v dokumentaci
### OPTIONS 
- je safe
- požadavek na informace o možnostech zdroje ([[URL, URI, URN, IRI#URI|URI]])
	- zjištění, jaké HTTP metody mohu používat
- dá se použít jako `ping`
### [[HTTP protokol#Stavové kódy|Stavové kódy]]

Jak řešit jméno v databázi?
- někdy je lepší mít jenom text "name"
- nějaké kultury mají více jmen, některé nemají příjmení, někde to mají opačně apod.

kód 412 - záleží na implementaci na serveru (někdy to vrátí, zkombinuje to na základě cache, co se upravovalo atd.)