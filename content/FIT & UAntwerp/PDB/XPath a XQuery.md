[[XPath a XQuery - cheatsheet]]
### XDM
- = XQuery and XPath Data Model
- is represented by an XML tree consisting of nodes
	- root node, elements, literals (text, numbers etc.), attributes 
	- XPath and XQuery are then traversing this tree
		- the document order determines the order in which nodes appear in the XML file
		- e.g. pre-order depth-first traversal
- each query expression is evaluated into a sequence
	- = ordered collection of nodes and/or literals
	- the values are automatically flattened (I won't see the original hierarchical structure, just results), duplicate items are allowed, can be empty `()` 
# XPath
- umí vybírat části [[FIT & UAntwerp/PDB/XML]] dokumentu na základě hierarchické cesty
- příklady:
	- absolutní cesta:
		- `/` nebo `/movies` nebo `/movies/movie/actor`
		- vždy z root elementu
	- relativní cesta - podle specifikovaného context elementu (např. v rámci podmínky)
		- `actor/name/text()`
	- `//autor` vybere všechny elementy `<autor>` v dokumentu
		- není to tak efektivní + pokud chci jenom specifické nody, tak je lepší absolutní/relativní cesta
		- ale naopak se to hodí pro rekurzivní struktury (např. struktura složek)
		- `.//` hledá pouze v relevantním subtree (např. v podmínkách)
			- `/airport/flights/flight[.//@class = "F" or .//@class = "C"]`
- každý krok vyhodnocení dotazu má 3 části: axes, node test and predicate
### Axes (osy)
- procházení pomocí specifikované cesty (path) simuluje procházení stromu (vzhledem ke kontextu, to je uzel "výše")
- mohu definovat jednotlivé osy procházení a podle toho vybírat uzly (vzhledem k aktuálnímu uzlu)
	- `/movies/child::movie` - všechny nody "movie", které jsou children kontextu: node movies
		- toto je default osa, nemusí se zmiňovat (tj. `/movies/movie`)
	- `/movies/movie/attribute::year` - vybírá atribut year daného context node (tedy movie)
		- tato jediná osa dokáže vybírat atributy 
		- může být zkráceno na: `/movies/movie/@year`
	- existuje 13 os včetně:
		- self, parent, ancestor, descendant, preceding-sibling, following-sibling, ancestor-or-self, descendant-or-self, following, preceding, namespace
	- self
		- vybere sám sebe (jako context node)
		- zkratka: `.//actor`, tečka je `self::node()` 
	- parent
		- vybere parent node daného context node
		- `parent::node()` nebo `/../`
	- descendant(-or-self)
		- vybere všechny nodes v podstromu pod daným context node (včetně/bez context node)
		- zkratka: `//actor` - `/descendant-or-self()::node()/actor`
			- tady je context node root `/`
	- ancestor(-or-self) 
		- vybere všechny předky daného context node (bez/se samotným context node) až do root node
	- preceding-sibling a following-sibling
		- vybere všechny předchozí nebo následující sousední nodes (podle document orderingu), které mají společného rodiče
	- preceding a following
		- vybere všechny nodes, které se objeví před/po context node (kromě přímých rodičů a potomků)
	- ![[Pasted image 20260115135748.png]]
	- forward axes - nodes are returned in document order
		- self, child, descendant, following...
	- reverse axes - nodes are returned in reversed document order
		- parent, ancestor, preceding...
### Node tests
- druhý krok při výběru konkrétních elementů
- node test specifikuje, které uzly se mají kolem konkrétní osy vybrat (musí splňovat nějakou podmínku)
	- můžu vybrat nody podle názvu (`/movies`) nebo podle názvu atributu
		- `/movies`
		- `/movies/movie/attribute:year` - nody, které mají atribut `year` (konkrétní hodnoty řeší až predikáty)
	- pomocí wildcard `*` 
		- vybere všechny elementy
	- pouze textové nody (nody, které obsahují text, ne text samotný):
		- `/movies/movie/title/text()`
	- `node()` - vybere všechny typy nodů (elementy, text, komentáře apod.)
	- `comment()` - vybere pouze komentáře
- tečka znamená "aktuální node"
	- tj. `./` - ber to od aktuálního nodu (např. v podmínce)
	- `.//` - celý subtree pod aktuálním nodem
### Predikáty (podmínky)
- pokročilé filtrování pomocí dalších podmínek
- pokud jich je více za sebou, tj. `[...][...]`, tak se vyhodnocují postupným filtrováním zleva doprava
	- ale mohu mít jednu podmínku a v rámci té podmínky mít `and` nebo `or`
	- výsledek je výstup z posledního "kroku"
- příklad:
	-  `/movies/movie[actor]/title/text()` - pokud má movie nějaké actory, tak vyber title toho movie
		- pokud vrátí non-empty sequence, vyhodnotí se jako `true`
	- `/descendant::movie[@year > 2000]` - všechny movies, které jsou mladší než r. 2000
	- můžu také testovat na pozici (první X poslední element v rámci parent node)
		- `/movies/movie[1]`
		- `/movies/movie[last()]`
		- `/movies/movie[position() < 4]`
- comparisons
	- `=, !=, <, <=` atd. pro srovnávání sekvencí nodů
		- pro jakoukoliv délku, je `true`, pokud lib. část sekvence splní podmínku
		- může být teoreticky použito i pro dvě sekvence o jedné hodnotě
	- `gt, ne, lt, le, ge, eq` pro srovnávání konkrétních hodnot
	- `is` for node comparisons
	- `<<, >>` for node order comparisons
- výstup:
	- jako výstup může být sekvence nodů seřazená podle document order
		- duplicity jsou odstraněny
	- NEBO sekvence jednotlivých hodnot
		- duplicity zůstávají
- funkce na zobrazování v rámci FLWOR nebo node constructors:
	- distinct-values(XPath expression)
	- data(XPath expression returning only strings from attributes)
		- jednotlivé atributy nemohou být jen tak vráceny (mimo svoje elementy)
		- atomizování hodnot **atributů**
	- avg() - průměr
	- count() - počet nodů
	- string-join() - spojování textových hodnot
- je to stavební kámen pro [[#XQuery]] a [[FIT & UAntwerp/PDB/XML#XSLT|XSLT]]
# XQuery
- dotazovací jazyk pro [[FIT & UAntwerp/PDB/XML]]
- umožňuje vybírat části XML (pomocí [[#XPath]])
- umí dělat transformace a výpočty nad XML daty
- umí vytvářet nové XML dokumenty (podle dotazu či transformace)
	- např. vytvoření HTML tabulky z XML dat
- používá se v XML databázích
- možnosti:
	- path expressions
		- zajištěno pomocí XPath
	- comparison expressions 
		- pro porovnávání hodnot i nodů
		- pokud jsou použity obecné porovnání, tak se nody "atomizují" na hodnoty, které jsou uvnitř a hodnoty samotné zůstanou stejné
		- value comparison: `eq`, `ne`, `lt`, `le`, `gt`, `ge` - porovnává atomické hodnoty
		- general comparison: `=`, `!=`, `<`, `<=`, `>`, `>=` - porovnává sekvence (existenciální kvantifikace)
		- node comparison: `is`, `<<`, `>>` - porovnává identitu a pořadí nodů v dokumentu
	- node contructors
		- je možné vytvářet nové uzly, atributy, hodnoty apod. na základě již existujících
		- **direct constructors**: `<movies>{ count(//movie) }</movies>` - přímý zápis XML (prostě začnu psát daný element a do něj pomocí složených závorek XQuery zápis)
		- **computed constructors**: `element movies { count(//movie) }` - dynamické vytváření
			- `element` - for nodes
			- `attribute` - for attributes
			- `text` - for text nodes
	- FLWOR expressions
		- for, let, where, order by, return
			- for loop also contains optional positional variables, which provide an ordinal number of the current item
		- proměnné označuji jako v PHP (`$movies = //movie`)
		- umožňují pokročilou práci s cykly
	- switch expressions
	- conditional expressions
		- také jsou možné podmínky (if - then - else)
		- větev `else` je povinná
			- mohu vložit prázdnou sequence (`else ()`)
	- quantified expressions
		- používají se v rámci klauzule `where`
			- `some` - alespoň jeden
			- `every` - pro všechny
			- `some $a in //movie/actors satisfies $a = 'Barta'`
	- boolean expressions
		- including and, or, not... ve `where` klauzule
	- primary expressions
		- literály

```xquery
# Node constructor
<movies>
<count>{ count(//movie) }</count>
{
for $m in //movie
return
<movie year="{ data($m/@year) }">{ $m/title/text() }</movie>
}
</movies>

# Alternative syntax
element movies {
	element count { count(//movie) },
	for $m in //movie
	return
		element movie {
		attribute year { data($m/@year) },
		text { $m/title/text() }
	}
}

# FLWOR
# najdi titulky filmů natočených v roce 2000 a později, mají max 3 herce a rating je lepší než průměr
let $r := avg(//movie/@rating)
for $m in //movie[@rating >= $r]
let $a := count($m/actor)
where ($a <= 3) and ($m/@year >= 2000)
order by $a ascending, $m/title descending
return $m/title

# Conditions
if (count(//movie) > 0)
then <movies>{ string-join(//movie/title, ", ") }</movies>
else ()

# Quantified expressions
for $m in //movie
	where some $a in $m/actor satisfies $a = "Ivan Trojan" 
	return $m/title/text()

