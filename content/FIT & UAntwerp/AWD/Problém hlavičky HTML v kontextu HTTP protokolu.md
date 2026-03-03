Problém hlavičky dokumentu [[HTML]] (`<!DOCTYPE html>`) - je to až uvnitř toho dokumentu
- [[HTTP protokol]] neví, v jakém je to kódování, v jakém formátu to je (HTML, JS, obrázek, pdf...)?
- dá se odhadovat pomocí koncovky souboru, nebo pomocí archaické funkce `file` - která to "odhadne" pomocí prvních pár bytů souboru
	- to ale není 100% spolehlivé
### Řešení
Webový server s daty (soubory) posílá ještě svoji hlavičku s typem obsahu a dalšími parametry a pro double check se do [[HTML]] hlavičky může přidat:
```html
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
```
- toto je ekvivaletní zápis hlavičky HTTP přímo v dokumentu (pro double check)
	- pokud se to shoduje, není to problém
	- pokud se to neshoduje, preferuje se hlavička [[HTTP protokol]]u
- klient má za povinnost ignorovat obsah souboru a respektovat hlavičky
	- někdy se část hlavičky může zamlčet, aby si to klient mohl udělat podle sebe (co ale pak server "řekne", to platí)