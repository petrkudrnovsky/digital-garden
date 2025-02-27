- server se snaží vyhovět požadavkům a preferencím klienta
	- klient posílá hlavičky Accept* (od protokolu [[HTTP protokol#HTTP/1.1]]) - toto jsou dimenze dojednávání obsahu (postupně):
		- Accept - [[MIME type]], [[Moduly pro práci s MIME informacemi]]
		- Accept-Language - v jakém jazyce klient preferuje obsah
		- Accept-Encoding - pokud klient nepodporuje kódování, server pošle nezkomprimovaná data
			- můžeme si také nastavit úroveň komprese pomocí `DeflateCompressionLevel`
		- Accept-Charset - v jakém kódování klient preferuje obsah
	- to zajištuje modul **mod_negotiation**
- server si zjistí hlavičky, jak to "klient chce", eliminací vybere nejlepší variantu
	- pokud nedokáže namatchovat odpověď, pošle 406 - No acceptable representation
## Algoritmus pro dojednávání obsahu
1. nejdříve si pro každou rovinu (dimenzi) dojednávání (jazyk, kódování atd.) zkontroluje odpovídající hlavičku `Accept*` a její příslušnou kvalitu (tu si určuje klient, může určit, co preferuje více či méně)
2. procesem eliminace se vybere nejlepší varianta
3. pokud algoritmus dospěl k výsledku, tak ten se vrátí a pošle klientovi
4. pokud nezbyl žádný výsledek, který by odpovídal požadavkům klienta, tak se vrátí 406 - *No acceptable representation* 
## Proces eliminace
- postupně se aplikuji jednotlivé testy v pořadí, varianty, které se v daném testu nevyberou, jsou eliminovány
- pokud v nějakém testu už zbyde jedna varianta, ostatní testy se přeskočí a varianta se rovnou pošle
1. vynásobení kvality zdroje (pro daný [[MIME type]], nachází se v type-var souboru) a kvality příslušné dimenze (`qs` parametr z `Accept` hlavičky) a vyber ty s nejvyšší hodnotou
	- varianty s kvalitou `q=0` se explicitině vyřadí
	1. vyber varianty s nejvyšší kvalitou u jazyka
	2. vyber varianty s nejlepším pořadím jazyka v `Accept-Language` nebo `LanguagePriority`
	3. nejlepší media type
	4. nejlepší znaková sada
	5. nejlepší kódování
	6. nejmenší velikost obsahu
2. první možnost z těch, co zbyly, tak server pošle (buď podle Type-map a nebo ASCII pořadí)

Aby mohl webserver spustit úspěšný negotiation process, tak potřebuje mít informace o tom, jaké všechny varianty obsahu vlastně má k dispozici - to se dozví pomocí jednoho ze dvou přístupů - type-map a multiviews:
## Type-map
- jedná se o jeden z [[Zpracování požadavku od klienta na Apache httpd#Handlery|handlerů]] 
- soubor, který v sobě přehledně obsahuje varianty jmen souborů, mezi kterými může server vybírat
	- má koncovku `.var`, `qs` je kvalita zdroje
```text
URI: text.html

URI: text.html.cs
Content-Language: cs-cz
Content-Type: text/html; qs=0.8

URI: text.html.en
Content-Language: en-us
Content-Type: text/html; qs=1
```
- díky tomu klient nemusí v [[URL, URI, URN, IRI#URL|URL]] žádat o konkrétní variantu (`/text.html.cs`), ale může žádat jenom o `/text` - server se podívá do `text.var`, dozví se všechny varianty a podle [[#Algoritmus pro dojednávání obsahu]] vybere tu nejlepší
- pokud je zapnutý type-map a multiviews, tak má type-map prioritu
## Multiviews
- [[Apache httpd]] se snaží najít nejlepší soubor, který použít, pokud nemá přesně název souboru (třeba existuje více se stejným jménem) na základě informací z hlavičky
- jedná se o takové "smart" vyhledávání přípon, vyhledá si soubory, které vyhovují požadavkům a mezi nimi vybírá (spustí negotiation process)
	- požadavek je na `/help` a v adresáři se nachází více `/help.*` souborů
	- Apache s Multiviews dynamicky najde `help.en.utf8.html`, `help.cs.html` atd.
		- a tyhle soubory pak slouží jako množina souborů, které prochází procesem algoritmu pro dojednávání obsahu
- zapínám ho pomocí `Options +Multiviews` pro jednotlivé adresáře
- když klient zažádá o např. `/help`, tak si server sám najde příslušné soubory (`help.en.html, help.cs.html` atd.) a pak mezi nimi podle [[#Algoritmus pro dojednávání obsahu]] vybere nejlepší
	- informace zjišťuje z přípon a z hlaviček
- hodí se, když chci nabízet různé verze/formáty stejného obsahu
- nemusím spravovat `.var` soubory

