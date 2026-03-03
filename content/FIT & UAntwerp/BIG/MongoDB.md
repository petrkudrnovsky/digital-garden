[[Dokumentově orientovaná databáze]]
- data ukládá v BSON (binární JSON)
### Architektura
- máme server, databáze, kolekce (logický kontejner pro dokumenty, ekvivalent tabulky) a dokumenty (jsou ve formátu klíč-hodnota i s vnořenými a složitými strukturami)
- instance -> databases -> collections -> documents
	- collection sdružuje více dokumentů, většinou s podobnou strukturou
	- jeden dokument = 1 JSON object (jakkoliv složitý a velký)
##### Sharding
- automatická distribuce dat mezi více uzlů clusteru
- master-slave architektura
- na jednotlivých uzlech jsou shardy
	- shard je implementován jako **replikační set** pro zajištění dostupnosti
	- data se rozdělují pomocí sharding klíčů
- součástí jsou konfigurační servery, které udržují metadata o rozložení dat v clusteru
- součástí jsou Mongo Routery = příjímají dotazy od klienta a přeposílají je na konkrétní shardy + vrací data klientovi
##### Replikace
- v rámci replikačního setu máme primární uzel a sekundární uzly
	- primární přijímá všechny zápisy
	- sekundární obsahují kopie dat a slouží k obsluze dat na čtení
	- po selhání primárního uzlu je jeden ze sekundárních uzlů povýšený na primární
		- pomocí algoritmu volby (quorum)
##### Indexy
- optimalizace výkonu dotazů, redukují množství prohledávaných dat
- bez indexu se provádí *full collection scan* 
- máme jednoduché (na jednom poli dokumentu) a složené (na více polích najednou)
- multikey indexy - na pole obsahující pole nebo vnořené pole
	- mám pak index na každou hodnotu v poli
- textové indexy - pro fulltextové vyhledávání
- geospatiální indexy - na geolokační data
- pozor - indexy jsou náročné na místo na disku
### Datový model
- `_id` je rezervován pro primární klíč (unikátní v rámci jedné collection)
	- většinou je datovým typem `ObjectId`, speciální 12 bajtový BSON typ
	- ale datový typ může být cokoliv kromě JSON Array
	- je immutable
- `$` je rezervováno pro query operace (nesmí být na začátku nějakého identifikátoru)
	- identifikátory také nesmí obsahovat tečku `.` (ta se používá pro přístup ke vnořeným polím)
- názvy polí musí být unikátní
- není žádné explicitní schéma dat, ale vzhledem k použití v aplikacích tam nějaké implicitní schéma je (aby to mělo cca aspoň nějakou strukturu a dalo se to spolehlivě používat)
- 2 možnosti designu datového modelu:
	- denormalizovaný datový model
		- related data jsou v jednom dokumentu (jako vnořené subdokumenty)
		- dobré pro one-to-one nebo one-to-many vztahy
		- možné data redundancies, méně queries potřeba
	- normalizovaný datový model
		- related data jsou v různých dokumentech a jsou provázané pomocí "References"
			- např. `actors: [ObjectId("6"), ObjectId("15")]`
		- dobré pro many-to-many
		- lépe organizované, více queries potřeba
### Dotazy
- query language je JavaScript
	- dotazy se provádí vždy nad konkrétní collection
	- read dotazy vrátí tzv. "cursor", pointer na výslednou množinu dokumentů (tj. nenahrajou se rovnou do paměti, ale mám na ně ukazatel a můžu si je procházet)
		- tím pádem mohu za `find` řetězit `sort(), skip(), limit()`
- CRUD
	- inserting - pokud specifikovaná collection neexistuje, tak se vytvoří
		- vkládaný `_id` musí být unikátní, pokud tam není, tak se automaticky vytvoří `ObjectId`
	- replacing - pokud více dokumentů vyhovuje dotazu, tak se nahradí pouze první nalezený
		- dokument se nahradí celý
		- upsert mode - pokud se najde dokument -> replace, pokud se nenajde -> insert
			- `{ upsert: true }`
	- updating
		- vybere se dokument/dokumenty, které se budou aktualizovat
		- pak následuje seznam úprav:
			- používá se `$set`, `$inc`, `$unset`, `$rename` atd.
			- pro Arrays: `$push`, `$addToSet`, `$pop`, `$pull`
		- také existuje upsert mode
			- pokud se bude vytvářet, tak nový dokument bude obsahovat všechny hodnoty z query části, upravené o hodnoty ze seznamu úprav
	- finding
		- selection - když děláme selekci na shodnot hodnot, tak musí být úplně identické
			- dva typy:
				- value equality = porovnávání hodnot 
					- objekty musí být 100 % identické
						- i co se týče čísel, pořadí, názvy rekurzivních vnořených objektů apod.
				- query operators = na základě definovaných podmínek
		- projection
			- `_id` se nachází defaultně ve výsledku, lze ho odstranit pomocí `_id: false` or `_id: 0`
			- pole s `true/1` jsou included
			- pole s `false/0` jsou excluded
				- nesmí se to míchat dohromady, musím si vybrat, jestli chci vybírat ty, co budou included a nebo vybírat ty, co budou excluded
		- modifiers - mění pořadí a množství výstupních dokumentů
			- sort - sortování se provádí před projekcí (tj. nezobrazená pole mohou být použita k řazení)
				- 1 pro ascending, -1 for descending
			- skip - přeskoč X dokumentů od začátku odpovědi
			- limit
### Výhody
- flexibilita (různé dokumenty v jedné kolekci)
- škálovatelné - horizontální škálování pomocí shardingu
- výkon
- vnořené dokumenty - můžu reprezentovat složité struktury a nemusím joinovat
### Nevýhody
- nezaručená integrita dat
- nadbytek dat - můžou vznikat redundance
- složitější správa clusteru
- absence joinů - dotazování se napříč kolekcemi je složitější
### Validační schéma
- pokud chceme specifikovat různé datové typy, povinnost jednotlivých properties či rozsah hodnot
- úrovně vynucování validace:
	- strict - pokud dokument nevyhovuje validačnímu schématu - operace selže
	- moderate - operace selže jenom při explicitní změně nevyhovujících polí
		- takže můžeme vkládat dokumenty, které nevyhovují schématu
- díky validačnímu schématu máme kvalitnější data, minimum chyb (aplikace nepracuje s nevalidními daty) a lepší údržba (máme očekávatelný formát)
```json
db.createCollection("uzivatele", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["jmeno", "vek"],
      properties: {
        jmeno: {
          bsonType: "string",
          description: "Jméno uživatele musí být text"
        },
        vek: {
          bsonType: "int",
          minimum: 0,
          maximum: 120,
          description: "Věk musí být celé číslo v rozsahu 0–120"
        },
        email: {
          bsonType: "string",
          pattern: "^.+@.+$",
          description: "Email musí být validní"
        }
      }
    }
  }
});

```
### [[CAP teorém]]
- splňuje garanci CP