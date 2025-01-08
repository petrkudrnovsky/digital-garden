### Základy a pojmy
[[URL, URI, URN, IRI]]
### Doména
- je to jednoznačné ID počítače/serveru, je hierarchická
- ICANN je správce top-level domén. Každá doména má svoji autoritu. `.cz` doménu spravuje CZ.NIC.
- CZ.NIC definuje legislativní podmínky pro ty domény
	- pokud uvedu např. neplatné údaje vlastníka - tak mi to může zrušit
	- můžu se u nich na webu dozvědět podrobnosti o vlastnostech, expiracích a vlastnících domén
### Intranet
- soukromá síť (většinou ve firmách), ale využívá technologií internetu (prohlížeče, web servery)
- součástí je firewall, který takovou síť chrání před nevyžádanými příchozími požadavky + blokuje i nějaké odchozí (tomuto účelu může sloužit i [[Proxy#Forward proxy|forward proxy]])
### DNS
= domain name system, poskytuje metadata k dané URL
- slouží hlavně jako "překladač" domény na IP adresu a zpět
- existuje 12 kořenových systémů a při překladu se využívá rekurze, aby se zjistily IP adresy jednotlivých serverů, které znají další část domény
	- pokud server nezná celou IP pro danou URL, ptá se rekurzivně dalších serverů
		- začíná se od top-level domén (tj. nejdříve `.cz`, pak `cvut.cz` a pak `fit.cvut.cz`)
	- efektivně se využívá cachování (proto se změny v DNS projeví až po nějaké době)
### [[Proxy]]
### [[HTTP protokol]]

