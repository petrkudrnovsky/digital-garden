Je to technika, která umožňuje na jednom webovém serveru hostit více domén najednou.
- přichází s [[HTTP protokol#HTTP/1.1]]
- direktiva `<VirtualHost>`
## IP-based virtual hosting
- pro každou hostovanou doménu je vyhrazena **nesdílená** IP adresa nebo port (ale většinou ty IP adresy volné nemáme)
- je možné použít i v protokolu HTTP/1.0
- pokud se nenajde odpovídající IP adresa, tak se použije `_default_`, pokud není default, tak se použije nastavení hlavního serveru 
```
<VirtualHost 172.20.30.40>
    DocumentRoot "/www/example1"
    ServerName www.example.com
</VirtualHost>

<VirtualHost 172.20.30.50>
    DocumentRoot "/www/example2"
    ServerName www.example.org
</VirtualHost>
```
## Name-based virtual hosting
- na **jedné** IP adrese/portu se hostuje více domén - rozlišují se podle jména serveru (`ServerName` nebo `ServerAlias`)
- musí se implementovat mechanismus pro rozlišování jednotlivých domén
	- HTTP/1.1
		- pokud dostane absolutní [[URL, URI, URN, IRI#URI|URI]], musí hlavičku `Host` ignorovat a použít název hostitele z [[URL, URI, URN, IRI#URI|URI]]
		- pokud dostane relativní [[URL, URI, URN, IRI#URI|URI]] a požadavek obsahuje hlavičku `Host`, tak použije název z hlavičky `Host`
		- jinak odpoví 400 - Bad Request
- nebo můžeme použít `<VirtualHost *:80>`, který bere všechny IP adresy a rozhoduje se podle `ServerName` nebo `ServerAlias`

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
> Jednotlivé techniky lze kombinovat. Tj. můžu mít 2 VirtualHosty na stejnou IP adresu a pak u nich mít rozdílný `ServerName`.
> Všechny příklady najdu zde: [VirtualHost Examples - Apache HTTP Server Version 2.4](https://httpd.apache.org/docs/2.4/vhosts/examples.html)
## Výběr virtuálního hostitele
![[Pasted image 20250111173836.png]]
- pokud nám sedí IP adresa, tak se koukáme na ServerName a ServerAlias, jestli nám to nematchne hlavičku `Host`