= [[Javascript]] Object Notation
- lehký, populární formát pro přenos a uchovávání dat
	- jednoduše se parsuje a zároveň je čitelný pro lidi
- není závislý na jazyku, většina programovacích jazyků ho umí zpracovávat
- BSON - JSON v binárním reprezentaci
	- výhoda je v rychlejším procházení dokumentu či v možnosti využívat další datové typy, které nejsou v normálním JSONu (např. binární objekty)

Příklad:
```json
{
  "jmeno": "Karel",
  "vek": 30,
  "zajmy": ["fotbal", "programování"],
  "jeStudent": false
}
```

Zajímavé datové typy:
- Array - umí uchovávat hodnoty s různých typů
- Object - neseřazená množina key/value párů
- root JSON souboru může být buď Object, Array a nebo jedna jediná hodnota
# JSON Schema
- řeší strukturu, validaci a anotaci dat + slouží jako dokumentace dat
- mohu specifikovat 
	- datové typy
	- povinné atributy (pomocí `required`)
	- vzory a formáty - např. regex pro e-mailovou adresu či pro [[URL, URI, URN, IRI|URL]]
	- pomocí `$ref` mohu referencovat i externí schémata
		- například pro zeměpisnou šířku/délku nemusím psát svoje Schema, ale mohu využít již existující
- moho anotovat (jednotlivé objekty)
	- přidám `title`, `description` atd.
- příklad:
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "Osoba",
  "type": "object",
  "properties": {
    "jmeno": {
      "type": "string"
    },
    "vek": {
      "type": "integer",
      "minimum": 0
    },
    "zajmy": {
      "type": "array",
      "items": {
        "type": "string"
      }
    }
  },
  "required": ["jmeno", "vek"]
}
```
# JSON-LD
- = JSON for Linked Data, propojení s [[RDF]], je to rozšíření pro JSON, díky kterému jsme schopni zapsat RDF data v JSONu
![[Pasted image 20250305160755.png]]
- cílem JSON-LD je sjednotit práci nad daty pro JSON i [[RDF]] orientované vývojáře
	- RDF vývojáři si mohou nahrát JSON-LD soubor jako další [[RDF]] serializaci a bude jim to fungovat
	- JSON vývojáři pouze ignorují `@context` a pracují s normálním JSONem
- pomocí části `@context` se dají vložit metadata a kontext tak, aby byla data navzájem propojitelná (pro stroje) podle principů Linked Data
- využití JSON-LD
	- v Schema.org, Google Knowledge Graph, SEO

# Využití JSON
- API
- konfigurační soubory
- záznamy logů
- databáze
	- CouchDB, [[MongoDB]]