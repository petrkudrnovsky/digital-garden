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
## [[Certifikát a certifikační autorita]]