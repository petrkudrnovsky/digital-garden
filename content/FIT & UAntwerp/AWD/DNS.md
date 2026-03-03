- *domain name system*
- = systém určený pro překlad doménových jmen na IP adresy a naopak
	- je hierarchický
	- není zabezpečený ([[#DNSSEC]] je zabezpečená varianta)
	- důvod zavedení: lidem se špatně pamatovaly [[IP protokol|IP adresy]] 
- běží na portu 53 a používá [[UDP protokol]]
- funguje na [[ISO OSI model#Transportní vrstva|transportní vrstvě]] 
- každá stanice má nastavenou adresu pro DNS server (buď staticky nebo ji získá dynamicky pomocí [[DHCP - dynamická konfigurace IPv4|DHCP]])
- při potřebě překladu se OS obrátí na [[#DNS Resolver]], který mu zařídí překlad dané adresy
- DNS servery jsou hodně cachované pro urychlení překladu
### Jak to funguje?
1. zadám pro prohlížeče `www.google.com`
2. OS se podívá do svého souboru `hosts.txt`, kde má uložené překlady na IP adresy
3. pokud neuspěje, zeptá se svého DNS Resolvera (specifikovaný v konfiguraci OS)
4. DNS Resolver se buď iterativně nebo rekurzivně snaží najít hledanou IP adresu pro dané doménové jméno
##### Iterativní dotazování v DNS
![[Pasted image 20250504183110.png]]
##### Rekurzivní dotazování v DNS
![[Pasted image 20250504183151.png]]
### Hierarchie domén a doménových jmen
- doménová jména mají stromovou strukturu
- `www.timetable.fit.cvut.cz`
	- kořenová doména: `.cz` (úroveň zanoření 1)
	- subdoména: `cvut.cz` (úroveň zanoření 2)
		- subdoména subdomény: `fit.cvut.cz` (úroveň zanoření 3)
	- definice domény = množina různých doménových záznamů sdružených pod společným doménovým jménem
		- každá doména má svůj hlavní DNS server, který spravuje její konkrétní záznamy ([[#Doménový zdrojový záznam]])
- kořenové DNS servery
	- jsou rozdělené do 13. skupin po celém světě, udržují informace o DNS serverech domén 1. úrovně (TLD domén)
	- požadavky na kořenové servery se posílají anycastem (tedy hledám prvního, kdo mi odpoví)
	- tyto servery jsou v podstatě nahardcodované do OS, takže jejich IP adresy jsou dopředu známé
- DNS servery pro TLD (*top level domain*)
	- to jsou: 
		1. Generic Top Level Domains - `.gov`, `.edu`, `.com` atd.
		2. Country Code Top Level Domains - `.cz`, `.de` atd.
		3. New Generic Top Level Domains - `.paris`, `london` atd. - může to být libovolný řetězec
### Doménový zdrojový záznam
- záznam, který obsahuje konkrétní informace pro konkrétní doménové jméno
- mají různé typy:
	- ![[Pasted image 20250504182316.png]]
### DNS protokol
- slouží pro zasílání dotazů a odpovědí v systému DNS
- většinou se používá [[UDP protokol]], port 53
- formát paketu je stejný pro dotaz i odpověď (v jednom paketu může být více dotazů i odpovědí)
### Reverzní dotaz
- opačný dotaz, pro danou IP adresu potřebuji doménové jméno
- funguje stejně, jako přímý dotaz s následujícími změnami:
	- za hledanou IP adresu se přidá `in-addr.arpa`: `DD.CC.BB.AA.in-addr.arpa`
	- finální reverzní překlad provede DNS server organizace, která má adresní rozsah na starosti
### DNSSEC
- založeno na [[Asymetrická kryptografie]]
- každý DNS server svoji odpověď podepíše svým soukromým klíčem
	- v nadřízeném DNS serveru je uložen veřejný klíč daného (podřízeného) serveru a tím jde ověřit pravost odpovědi
### DynDNS (dynamické DNS)
- pro zařízení, které často mění svoji IP adresu -> klasické DNS záznamy jsou nepoužitelné
- DynDNS server dokáže měnit A záznamy podle pokynů jednotlivých stanic
	- zařízení se připojí do sítě a místnímu DynDNS serveru nahlásí svoji IP adresu a doménové jméno
	- ostatní zařízení v síti se ptají DynDNS serveru na doménové jméno, dostanou uloženou IP adresu
	- kontrola dostupnosti zařízení se pravidelně opakuje (např. každých 10 minut)
### DNS Cache poisoning
- kybernetický útok, jehož cílem je vložit falešné záznamy do DNS resolveru (resp. do jeho cache), aby pak vracel podvodné servery i v případě, když uživatelé zadají správné URL
- ochranou je DNSSEC a krátké TTL záznamů v cache