[[Dokumentově orientovaná databáze]]
- data ukládá v BSON (binární JSON)
### Architektura
- máme server, databáze, kolekce (logický kontejner pro dokumenty, ekvivalent tabulky) a dokumenty (jsou ve formátu klíč-hodnota i s vnořenými a složitými strukturami)
##### Sharding
- automatická distribuce dat mezi více uzlů clusteru
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