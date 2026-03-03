- = eXtensible Markup Language
- značkovací jazyk, definovaný konsorciem W3C, podobný [[HTML]] (dokonce máme [[XHTML]])
- content type: `text/xml`
- je hierarchický ([[MDF - Hierarchické datové formáty - XML a JSON. Dobře formovaný (well-formed) a validní (valid) XML dokument. XSD a JSON Schema. Formát JSON-LD#Co je hierarchický datový formát?|Co je hierarchický formát?]]), dodržuje stromovou strukturu
- tagy nejsou definované (jako v [[HTML]]), ale dá se do nich napsat cokoliv
- důležitá je deklarace specifikující verzi XML a kódování dokumentu
	- `<?xml version="1.0" encoding="UTF-8"?>`
		- existuje i verze `1.1`, ale moc se nepoužívá
		- `UTF-8` je default, nemusím ji zmiňovat
- **musí** se začít jedním root elementem, který obaluje zbytek
	- u jednotlivých elementů záleží na pořadí
	- názvy elementů jsou case-sensitive
- máme 2 hlavní druhy XML:
	- dokumentově orientované XML
		- lidsky čitelný text, důležité části jsou otagované
	- datově orientované XML
		- strukturované uspořádání dat
		- typicky vytvářený a konzumovaný různými aplikacemi
		- už není tak dobře lidsky čitelný, slouží primárně strojům
- dotazovací jazyk je [[XPath a XQuery|XQuery]] a XPath (který je součástí XQuery)
### Účel
- primárně pro ukládání a přenos dat (semi-strukturovaných)
	- ukládání v XML databázích
	- přenos např. v rámci [[SOAP]]
- je samopopisující (spolu s daty je v tagu uložena informace o tom, co je to za data)
- má velký ekosystém a dobré standardy - proto se narozdíl od JSONu používá v kritických systémech (vládní systémy, korporátní systémy)
	- JSON se používá spíš na webu
- použití také v:
	- [[XHTML]], ePUB, SVG, RSS, SOAP
# [[XPath a XQuery]]
# Well-formed XML
- = pokud splňuje pravidla daná XML (specifikace stanovené W3C)
	- pouze jeden kořenový element
	- každý otevírací tag musí mít svůj uzavírací tag (a to ve správném pořadí otevírání)
	- [[#Namespaces|jmenné prostory]] musí být korektně deklarovány a konzistentní v dokumentu
	- atributy v rámci jednoho tagu se nesmí opakovat (např. 2x `id`)
	- atributy musí být v uvozovkách
	- speciální znaky musí být zakódované (např. `&amp;` místo `&`)
- kontroluje se XML Parserem
# Namespaces
- díky namespaces se můžeme vyhnout problémům s duplicitními tagy
	- když např. kombinujeme XML z různých zdrojů/aplikací do sebe
- definují se pomocí `xmlns` atributu (dostáváme tzv. "kvalifikovaný namespace")
	- syntax: `xmlns:prefix="URI"`
- velmi často se všechny namespaces definují v root elementu
```xml
<root>  
  
<h:table xmlns:h="http://www.w3.org/TR/html4/">  
  <h:tr>  
    <h:td>Apples</h:td>  
    <h:td>Bananas</h:td>  
  </h:tr>  
</h:table>  
  
<f:table xmlns:f="https://www.w3schools.com/furniture">  
  <f:name>African Coffee Table</f:name>  
  <f:width>80</f:width>  
  <f:length>120</f:length>  
</f:table>  
  
</root>
```
# Valid XML
- validní XML je [[#Well-formed XML]] a k tomu splňuje pravidla definovaná ve schématu (např. [[#XSD Schema]], RELAX NG nebo DTD (Document Type Definition))
- ve schématu specifikujeme pravidla, elementy a atributy, které se mohou v dokumentu používat
# XSD Schema
- používá se na specifikování struktury a datových typů pro XML dokumenty
- můžu definovat jaké elementy mají v dokumentu být
	- a jaké datové typy mají obsahovat
- při validaci parser ověří, že je dokument well-formed a zároveň splňuje pravidla schématu
### XSD Schema
```xml
<?xml version="1.0" encoding="UTF-8"?>
<xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema">

  <xsd:element name="kniha">
    <xsd:complexType>
      <xsd:sequence>
        <xsd:element name="titul" type="xsd:string"/>
        <xsd:element name="autor" type="xsd:string"/>
        <xsd:element name="rokVydani" type="xsd:integer"/>
      </xsd:sequence>
    </xsd:complexType>
  </xsd:element>

</xsd:schema>
```
### Dokument
```xml
<?xml version="1.0" encoding="UTF-8"?>
<kniha xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:noNamespaceSchemaLocation="kniha.xsd">
  <titul>Moje kniha</titul>
  <autor>Jan Novak</autor>
  <rokVydani>2020</rokVydani>
</kniha>
```
# XSLT
- používá se k transformaci XML dokumentů do jiných formátů (může být i do XML, ale i [[HTML]], JSON, prostý text atd.)
	- např. mám XML se seznamem knih a chci vytvořit HTML tabulku s formátovaným výpisem dat z XML 
- pomocí [[XPath a XQuery|XPath]] se vybírá na jaké uzly se transformace vztahuje
