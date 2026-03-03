Obecně o [[MIME type]] zde.

Při přenášení souborů chceme s nimi mít i informaci, jak tyto soubory zpracovávat a interpretovat. Pomáhají nám v tom hlavičky:
- `Content-Type: image/gif`
- `Content-Language: cs, en`
- `Content-Type: text/html; charset=ISO-8859-2`
- `Content-Encoding: x-gzip`
	- udává obecně kódování dat (ne textu)
### Obecně
- název modulu: **mod_mime**
	- existuje také **mod_mime_magic**, umí podle prvních pár bytů určit MIME typ, pokud to nedá mod_mime (ale zatěžuje to server)
- když změním MIME informaci u souboru, tak `Last-Modified` se nezmění
- můžu mít více přípon
	- MIME typ se bere podle poslední přípony
	- ostatní přípony slouží pro další metainformace (např. jazyk, kódování apod.)
		- např. soubor `info.cs.utf8.html`
- na serveru je soubor, kde jsou všechny "pro server známé MIME types" definované (direktiva `TypesConfig`)
	- mohu je přidávat pomocí `AddType image/gif .gif`
- můžeme i forcovat MIME types
### Prioritizace
![[Pasted image 20250111185836.png]]