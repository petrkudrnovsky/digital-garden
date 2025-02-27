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
- certifikát obsahuje veřejný klíč CA a informace identifikující majitele a vydavatele
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
	- klient se při připojení na server podívá na CA a ověřuje, jestli tuto CA (nebo řetězec vedoucí k root CA) uznává nebo ne (tj. jestli je server důvěryhodný nebo ne)
- self-signed certifikát
	- není vydán žádnou oficiální CA, takže je z principu nedůvěryhodný
	- spíš na interní účely a testování
	- můžu se s nějakými entitami domluvit, že si budeme věřit a přidáme si svoje certifikáty do seznamu certifikátů, kterým věříme
		- např. tohle se používá v rámci organizací
		- jsem student ČVUT, budu věřit autoritě ČVUT
	- udělá se tak, že si vygeneruju ještě jeden pár private key, public key a tím si podepíšu svůj certifikát
#### Formát uložení certifikátu
- `DER` - přímo binární data (např. MS Outlook)
- `PEM` - zakódovaný v base64 (ASCII znaky)
	- používá ho [[Apache httpd]], OpenSSL
	- přenáší se přes protokoly SMTP, [[HTTP protokol]]
#### Řetězec důvěry (chain of trust)
- kořenová certifikační autorita - těch je na světě pár a klient má uložené kořenové certifikáty autorit (v prohlížeči nebo v OS), které uznává
	- pak se díky řetězci důvěry dostane od ověřované CA ke kořenové a pokud ji uznává, tak je pro něj daný server důvěryhodný
#### Certifikační autorita
- vydává certifikáty ostatním, před vydáním certifikátu ověří dané informace v něm
- pak vypočítá hash informací v certifikátu a tu hash zašifruje (podepíše) pomocí svého soukromého klíče
	- takže pak klient si dekóduje certifikát z base64, podívá se na podpis a ten dešifruje pomocí veřejného klíče té CA, která to podepisovala - pak si spočítá taky hash informací z certifikátu a pokud se obě hashe shodují, tak víme, že ten certifikát byl opravdu podepsát danou CA
- kořenová CA je vlastně self-signed, protože už ji nemá kdo ověřit
	- musíme jim prostě věřit
#### Klientský certifikát
- někdy potřebuje i server ověřit klienta (tedy opačně)
	- to se děje často ve firemních a korporátních prostředích - zaměstnanci se mohou svými certifikáty autentizovat
	- místo loginů se mohou používat klientské certifikáty
- existuje i dvoustranné TLS - kdy se vyžadují ověřené certifikáty na obou stranách
## V čem je ta bezpečnost?
- před začátkem komunikace si klient vyžádá certifikát serveru
	- ten je podepsaný nějakou CA
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
### Jak to funguje?
Hezky je to popsané zde: https://www.youtube.com/watch?v=T4Df5_cojAs&ab_channel=kubucation

1. mám server a chci mít valid certifikát
2. požádám ověřenou CA, aby mi ho podepsala
3. CA se na to podívá, ověří údaje, jeslti to sedí a podepíšou to
4. podpis udělají takhle: vypočítají si hash z textového obsahu toho certifikátu a tu zašifrujou svým soukromým klíčem
5. a ten podpis přidají k tomu certifikátu a dají ti ho, ty ho pověsíš na server

6. přijde klient, který si s tvým serverem chce povídat a chce ověřit, jeslti si povídá opravdu s tvým serverem a ne s nikým jiným
7. pošle první zprávu (jmenuje se ClientHello), kde pošle, jaký TLS verze umí, jaký šifrovací metody umí atd.
8. tvůj server odpoví tím, co budete používat (verzi TLS, šifrovací algo atd.) a ZÁROVEŇ pošle svůj certifikát (což je teďko prostě textový dokument s tím připlácnutým podpisem)
9. klient si ten certifikát přečte (je to textový soubor) a zjistí, jestli má tu CA v seznamu uložených důvěryhodných CA
10. pokud ano, tak vezme veřejný klíč té CA a dešifruje s ním ten podpis -> dostane hash
11. zároveň si ten klient vedle zahashuje ten obsah certifikátu a tyhle dvě hashe porovná
12. pokud se rovnají, tak je jasný (z principu jak asynchronní šifrování funguje), že tento certifikát byl opravdu podepsaný tou CA
13. klient tedy může v klidu důvěřovat informacím z toho certifikátu
14. oba si pak domluví nějaký symetrický klíč, kterým budou šifrovat další komunikaci
	1. to se dělá tak, že klient vygeneruje secret, který zašifruje veřejným klíčem serveru a pošle mu to
	2. server si to dešifruje svým soukromým klíčem a teďko jenom tyto dva počítače znají tento secret a na základě toho si mohou začít šifrovat zprávy
15. a začnou si povídat v HTTPS