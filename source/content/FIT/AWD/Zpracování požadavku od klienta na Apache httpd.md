1) klient se spojí se serverem (přes TCP či TLS/SSL) a aplikují se [[#Vstupní filtry]]
2) server dostane adresu (kam klient chce přistoupit, např. "/" nebo "book/15") a přeloží [[URL, URI, URN, IRI#URI|URI]] na konkrétní soubor v souborovém systému
3) zkontroluje přístup (může se se mnou klient bavit?)
	- [[Autentizace]] uživatele
	- kontrola práv pro přístup uživatele ([[Autorizace]])
	- kontrola přístupu založená na jiných kritériích (podle IP adresy - IP whitelist např.) 
4) zjištění [[MIME type]] dotazu a výběr [[#Handlery|handleru]]
	- hlavička `Content-Type`, MIME nese víc informací než jenom typ obsahu (např. jazyky apod)
	- server pak může vybrat správnou jazykovou mutaci stránek (kterou má klient preferovanou) - viz [[Dojednávání obsahu v Apache httpd]]
5) fixups - co se doposud nestihlo udělat
6) spuštění handleru a generování výstupu - tohle je reálné vyřízení požadavku příslušným handlerem
7) aplikování [[#Výstupní filtry]] 
8) zaslání požadovaných dat klientovi (s příslušnými hlavičkami)
9) zalogování přístupu (požadavku a výsledku)

![[Pasted image 20250111190627.png]]
## Handlery
- když chci specifikovat custom chování pro různé požadavky/obsah (jakým způsobem bude požadavek zpracován a jak bude vygenerová obsah)
- je to mechanismus nebo funkce, která zpracuje tělo požadavku
- např. 
	- core handler v Apache - zpracuje statický soubor
	- zpracování dynamického obsahu (PHP Handlery) - mod_php
	- přesměrování na speciální moduly
- `AddHandler cgi-script .cgi` v [[Konfigurace Apache httpd]] nám řekne, že `.cgi` soubory bude zpracovávat CGI handler
## Filtry
- umí zpracovávat data před i po zpracování (např. kontrola, komprese, úprava hlaviček apod.)
- je to takový řetězec filtrů, kterým data protékají
#### Vstupní filtry
- zpracovávají příchozí požadavek (včetně vstupu metody POST), mění se a kontrolují se data DŘÍVE než se předají [[#Handlery|handleru]]
- např. úprava chunked zprávy do kontinuálního proudu, dekódování obsahu, kontrola škodlivého obsahu
- direktiva `SetInputFilter` mění nastavení
#### Výstupní filtry
- aplikují se na odpověď z [[#Handlery|handleru]] před tím, než se odpověď pošle klientovi
- např. komprimování obsahu, aby byl datový objem menší
	- chunked encoding (posílá obsah po částech)
	- upravuje hlavičky (např. na CORS, security hlavičky)
	- [[Generování a úprava obsahu v Apache httpd#SSI (Server Side Includes) (mod_include)|Server Side Includes]]
- direktiva `SetOutputFilter`
