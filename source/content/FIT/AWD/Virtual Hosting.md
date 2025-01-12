Je to technika, která umožňuje na jednom webovém serveru hostit více domén najednou.
- přichází s [[HTTP protokol#HTTP/1.1]]
- direktiva `<VirtualHost>`
## IP-based virtual hosting
- pro každou hostovanou doménu je vyhrazena **nesdílená** IP adresa nebo port (ale většinou ty IP adresy volné nemáme)
- je možné použít i v protokolu HTTP/1.0
- pokud se nenajde odpovídající IP adresa, tak se použije `_default_`, pokud není default, tak se použije nastavení hlavního serveru 
## Name-based virtual hosting
- na **jedné** IP adrese/portu se hostuje více domén - rozlišují se podle jména serveru (`ServerName` nebo `ServerAlias`)
- musí se implementovat mechanismus pro rozlišování jednotlivých domén
	- HTTP/1.1
		- pokud dostane absolutní [[URL, URI, URN, IRI#URI|URI]], musí hlavičku `Host` ignorovat a použít název hostitele z [[URL, URI, URN, IRI#URI|URI]]
		- pokud dostane relativní [[URL, URI, URN, IRI#URI|URI]] a požadavek obsahuje hlavičku `Host`, tak použije název z hlavičky `Host`
		- jinak odpoví 400 - Bad Request

```conf
<VirtualHost IP:PORT>
	ServerName B
	DocumentRoot /var/www/html/x
</VirtualHost>

<VirtualHost _default_>
	ServerName C
	DocumentRoot /var/www/html/y
</VirtualHost>

ServerName A
DocumentRoot /var/www/html/z
```

> [!info] Kombinace
> Jednotlivé techniky lze kombinovat
## Výběr virtuálního hostitele
![[Pasted image 20250111173836.png]]
- pokud nám sedí IP adresa, tak se koukáme na ServerName a ServerAlias, jestli nám to nematchne hlavičku `Host`