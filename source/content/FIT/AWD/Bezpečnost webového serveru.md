Řeší se hlavně zabezpečením komunikace pomocí HTTPS (tedy [[HTTP protokol]] přes [[#protokol SSL/TLS]]). Bezpečnost [[Autentizace]] se moc neřeší (protože už probíhá šifrovaná komunikace) - viz [[Autentizace a autorizace v rámci Apache httpd]].

Používá se PKI = public key infrastructure
- [[Asymetrická kryptografie]]
## Protokol SSL/TLS
- je to mezivrstva TCP/IP 
- SSL = secure sockets layer - protokol pro šifrování spojení
	- je starší, teď se více používá TLS
- TLS = transport layer security
	- probíhá tzv. "handshake", kdy si obě strany domluví šifrovací algoritmy a vymění si klíče
	- pak proběhne samotný přenos dat a konec
## HTTPS
- je to jenom [[HTTP protokol]], který běží nad [[#SSL/TLS]] šifrovanou vrstvou
- výchozí port není 80, ale 443
## Certifikáty a řetězec důvěry
- certifikáty jsou součástí komunikace přes protokol TLS
- certifikát obsahuje veřejný klíč subjektu a informace identifikující majitele a vydavatele
- certifikát serveru
	- subjekt - komu byl vydán
	- vydavatel - certifikační autorita (CA)
	- doba platnosti
	- administrativní informace - verze, sériové číslo apod.
	- informace o vlastníkovi ve formě DN = distinguished name
		- to jsou informace navíc k certifikátu, je to jednoznačný řetězec, který popisuje identitu vlastníka
		- nejdůležitější je `CN = common name` - většinou doménové jméno
		- `O = organization`
		- `OU = organizational unit`
		- `L = city/locality`
		- `ST = state/province`
		- `C = country`
	- klient se při připojení na server podívá na CA a ověřuje, jestli tuto CA uznává nebo ne (tj. jestli je server důvěryhodný nebo ne)
- self-signed certifikát
	- není vydán žádnou oficiální CA, takže je z principu nedůvěryhodný
	- spíš na interní účely a testování
	- můžu se s nějakými entitami domluvit, že si budeme věřit a přidáme si svoje certifikáty do seznamu certifikátů, kterým věříme
		- např. tohle se používá v rámci organizací
		- jsem student ČVUT, budu věřit autoritě ČVUT
#### Formát uložení certifikátu
- `DER` - přímo binární data (např. MS Outlook)
- `PEM` - zakódovaný v base64 (ASCII znaky)
	- používá ho [[Apache httpd]], OpenSSL
	- přenáší se přes protokoly SMTP, [[HTTP protokol]]
#### Řetězec důvěry (chain of trust)
- kořenová certifikační autorita - těch je na světě pár a klient má uložené kořenové certifikáty autorit, které uznává
	- pak se díky řetězci důvěry dostane od ověřovaného CA ke kořenovému a pokud ho uznává, tak je pro něj daný server důvěryhodný
#### Certifikační autorita
- vydává certifikáty ostatním, před vydáním certifikátu ověří dané informace v něm
- kořenová CA je vlastně self-signed, protože už ji nemá kdo ověřit
	- musíme jim prostě věřit
#### Klientský certifikát
- někdy potřebuje i server ověřit klienta (tedy opačně)
	- to se děje často ve firemních a korporátních prostředích - zaměstnanci se mohou svými certifikáty autentizovat
	- místo loginů se mohou používat klientské certifikáty
- existuje i dvoustranné TLS - kdy se vyžadují ověřené certifikáty na obou stranách
## V čem je ta bezpečnost?
- před začátkem komunikace si klient vyžádá certifikát serveru
	- ten se podepsaný nějakou CA
	- klient si zkontroluje:
		- platnost certifikátu
		- řetězec důvěry - to, že je kořenová CA v seznamu důvěryhodných certifikátů a všechny mezičlánky dávají smysl
		- shoda domény 
			- obsah hlavičky `Host` stejný jako `CN` (common name) certifikátu
			- ještě porovnává aktuální DNS jméno se jménem z certifikátu
				- k ochraně proti útoky typu Man-in-the-middle
- pak se vymění klíče podle vybraného šifrovacího algoritmu (handshake)
- a probíhá šifrovaná komunikace, útočník už vidí jen zašifrované zprávy ke kterým nemá přístup

- bezpečnost je závislá na kryptografických algoritmech, které se používají
	- RSA, Diffie-Hellman, Eliptická křivka, AES, DES, SHA256...
- a na verzi šifrovacího protokolu (tu je potřeba zvolit opatrně)
	- nejnovější verze SSL protokolu je nejbezpečnější, ale skoro s nikým si nepopovídám
	- ale zase nechci povolit úplně všechny (popovídám si se všemi, ale zase riskuju neduhy starších verzí)