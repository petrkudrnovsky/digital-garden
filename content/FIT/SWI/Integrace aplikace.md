= propojení izolovaných aplikací, které umožňuje sdílet/vyměňovat data a funkce

### Proč to zákazník dělá
- chce využít toho, že už nějaké aplikace, které dělají nějakou práci, má (investoval do nich)
- chce sjednocení a unifikaci dat a procesů napříč celou organizací

### Na jaké problémy můžeme narazit
- nespolehlivá či pomalá síť/komunikace
- různé datové formáty
- různé jazyky
- při změně jedné aplikace to může mít velké dopady na celou skupinu propojených aplikací

### Styly integrace
**Přenos souborů** - sdílená data se uloží do souboru a pošlou se konzumentovi
- možnost integrovat aplikace v různých jazycích a na různých platformách
- lze použít různé formáty souborů
- spíš se používá pro málo frekventované výměny - typické pro dávkové zpracování dat

**Sdílená databáze** - do ní se data ukládají
- výměna dat je rychlá
- je však nutné navrhnout schéma, které vyhovuje všem aplikacím a je zde problém se zamykáním záznamů (aby se nepřepisovaly atd.)
- když provedu změny v databázi, tak na to musí reagovat všechny aplikace
- možné problémy se zapouzdřením a konzistencí dat
- používá se omezení přístupu k datům - pohledy a oprávnění pro jednotlivé aplikace
- může porušovat princip zapouzdření

**Vzdálené volání procedur** - poskytovatel vystaví rozhraní nějakých svých procedur a ostatní aplikace je mohou využívat
- tím se sdílí funkcionalita a dodržuje se princip zapouzdření

**Zasílání zpráv** - všechny aplikace jsou napojeny na systém, který zprostředkovává zasílání zpráv
- a data si systémy mezi sebou vyměňují pomocí těchto zpráv
- je možná asynchronní komunikace - není nutné, aby dva komunikující systémy byly dostupné současně - to zajistí právě systém pro posílání zpráv

### Technologie pro standardizované sdílení mezi aplikacemi
#### SOAP (Simple Object Access Protokol)
- průmyslový standard
- XSD popisuje formát přenášených dat
- přenášená data jsou v XML
- má mnoho podpůrných standardů a je typicky používán pro integraci podnikových aplikací
- XML má výhodu v XSD (což je XML schéma - popisuje formáty dat)
	- XSLT - transformace do dalších formátů
	- XPath / XQuery - dotazování

#### REST (Representational State Transfer)
- alternativa k SOAP
- každý zdroj je identifikován svým URI
- definuje CRUD operace nad zdrojem
- využívá hlavně HTTP protokol (GET, POST, PUT, DELETE...)
- formát dat je JSON, XML
- typicky pro přenášení dat mezi serverem a GUI