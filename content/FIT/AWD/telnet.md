Používá se pro připojení k jiným serverům (web, DB, poštovní), vytváření TCP spojení, ladění sítí
Teď je nahrazován bezpečnějším SSH

Interaktivní síťová komunikace

`telnet [HOST] [PORT]`

```bash
telnet localhost 80
# naváže se spojení a pak mohu psát požadavky
GET / HTTP/1.1 
Host: localhost
```
