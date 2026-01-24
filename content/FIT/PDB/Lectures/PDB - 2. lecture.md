### Exam topics
- XML: constructs (element, attribute, text, …), content model (empty, text, elements, mixed), entities, well-formedness; document and data oriented XML
- JSON: constructs (object, array, value), types of values (strings, numbers, …); BSON: document structure (elements, type selectors, property names and values)
- RDF: data model (resources, referents, values), triples (subject, predicate, object), statements, blank nodes, IRI identifiers, literals (types, language tags); graph representation (vertices, edges); N-Triples notation (RDF file, statements, triple components, literals, IRI references); Turtle notation (TTL file, prefix definitions, triples, object and predicate-object lists, blank nodes, prefixed names, literals)
# [[XML]]
- tips:
	- empty elements are abbreviated: `<actors />` instead of `<actors></actors>`
	- it's preffered to use double quotes only
	- for specials signs always use escape sequences (so they don't conflict with the document flow)
		- `&lt` for `>` etc.
### Railroad diagrams
![[Pasted image 20250930181422.png]]
- they describe a structure and content of some language, string etc.
- they represent something like a finite automat
- orange elements are "placeholders" for elements that contain more complex logic (and they can be described by another railroad diagram)
***
# [[JSON]]
***
# [[RDF]]