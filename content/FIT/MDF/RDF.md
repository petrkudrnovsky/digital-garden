Prezentace od p. Klímka na MDF [zde](https://docs.google.com/presentation/d/1p65vUsNHxDnY0D-nkUCiMUapigGlhPaLNoJL1pAmBWo/edit#slide=id.p).

RDF = Resource Description Framework
- jedná se o grafový datový formát
- základ jsou trojice, RDF je zjednodušeně množina těchto trojic:
	- subject
	- predicate
	- object
- pořadí trojic není definované (mohou být na přeskáčku) - je to dáno tím, že je to MNOŽINA

![[Pasted image 20250228215223.png]]
- toto nám říká:
	- stránka `index.html` byla vytvořena zaměstnancem číslo 8574
### Typy zdrojů (Resources)
- nějaký zdroj - definovaný svým [[URL, URI, URN, IRI|URI]] nebo IRI
- literál - je to nějaký string, prostý text
	- můžeme k němu přidat language tag (`@en`...)
	- musíme specifikovat jeho datový typ (default je `xsd:string`)
		- další typ je třeba `xsd:date`
		- i datové typy mají svoje IRI
- blank node - zdroj/věc bez IRI, má speciální ID, které je použitelné jenom v rámci toho stejného dokumentu
### Prefixy
- můžeme si definovat prefixy pro často používané zdroje (také pro přehlednost a čitelnost)
	- např. `@prefix dcterms: <http://purl.org/dc/terms/> .`
		- a pak mohu používat už jenom `dcterms:creator`
	- můžeme použít jakékoliv název pro prefix, ale jsou zavedené "známé" prefixy, které se používají
### Třídy
- třída sama o sobě je Resource
- používáme predicate `rdf:type` pro přiřazení nějakého zdroje do třídy
	- `my:staff/85740 rdf:type my:Person .`

RDF je OWA (= Open World Assumption) - to znamená, že pokud to neví pravdivost nějakého statementu, tak to odpoví "unknown"
- narozdíl od např. SQL, které odpoví "false"
- v rámci OWA, pokud chci, aby něco neplatilo, tak to musím explicitně specifikovat
***
# Serializace
### N-Triples
- jedna z prvních serializací, umí jen celá IRI (nejdou zkrátit)
- pros
	- jednoduchá, jednoduše se parsuje, jednoduše se píše
	- může je streamovat
- cons
	- lidsky je to špatně čitelné
	- zabírá hodně místa (zvlášť nekomprimovaná)
### Turtle
- zavádí prefixy, takže je zápis mnohem lépe čitelný pro lidi
	- existují i prefixy, které mají prázdný název
- zavádí `;` na konec řádku
	- znamená to, že Subject je stejný jako na řádku výše
- zavádí `,` na konec řádku
	- znamená to, že další RDF trojice bude mít stejný Subject i Object
- zavádí multiline strings (pomocí `"""`) a escapované sekvence (`\t, \n, \"...`)
- datové typy už to umí odvodit samo (4, 135.154, 1.3e6, true)
	- už nemusím za každý psát `^^xsd:double` 

```turtle
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .  
@prefix dcterms: <http://purl.org/dc/terms/> .  
@prefix my: <http://example.com/> .  
@prefix staff: <http://example.com/staff/> .  
  
my:index.html dcterms:created "2020-04-23"^^xsd:date  ;
              dcterms:creator staff:8574  ,
                              staff:8575  ;
              dcterms:title "Moje stránka"@cs  ,
                            "My page"@en  .
```
- syntaktická zkratka
	- `rdf:type` je zkráceno na `a`
		- `<http://example.com/index.html> a foaf:Document .`
		- protože se to čte `index.html` is ==a== document
### N-Quads
- založeno na [[#N-Triples]], přidává podporu pro pojmenované grafy
- S P O G
	- subject, predicate, object, graph
- čtvrtý graf slouží jako kontext k původní trojici
	- `<subjekt> <predikát> <objekt> <jméno_grafu> .`
	- `<http://example.org/petr> <http://schema.org/name> "Petr" <http://example.org/graph1> .`
	- kontext je přidaný kvůli lepší organizaci dat do různých celků
		- [[#Turtle]] a [[#N-Triples]] pracují celé v jedném množině, v jednom kontextu
### TriG
- inspirované [[#Turtle]], také přidává možnost pojmenovaných grafů
- grafy už máme vizuálně oddělené, jinak umí vše, co [[#Turtle]]
```turtle
@prefix ex: <http://example.org/> .

# defaultní graf
{
  ...
}

ex:graph1 {
  ex:petr ex:hasName "Petr" .
  ex:petr ex:hasAge 25 .
}

ex:graph2 {
  ex:petr ex:likes "Bike riding" .
}
```
***
# RDFS = RDF Schema
- můžeme definovat hierarchii tříd
	- pomocí `rdfs:Class` nebo `rdfs:subClassOf`
	- postupně definujeme Resources jak chceme aby byly hierarchicky pod sebou
	- pomocí `rdfs:subClassOf` můžeme řešit dědičnost
![[Pasted image 20250228223636.png]]
- můžeme také definovat properties (což jsou predikáty pro popis vztahů mezi Subjektem a Objektem)
	- a k nim i podvlastnosti `rdfs:subPropertyOf`
- `rdfs:label` - lidský popis zdroje (Resource)
- `rdfs:comment` - delší popis či komentář ke zdroji
- `rdfs:seeAlso` - ukazuje na další zdroj, který může nabídnout další/rozšiřující info
	- každá třída a property by měla mít tyto properties, je to základ pro kvalitní RDF Vocabulary
***
RDF je také propojitelné s [[JSON]], konkrétně pomocí [[JSON#JSON-LD]] 
