Ve zkratce je to grafická vizualizace logické hierarchie z [[Logická architektura v UCD]].

Hodně se řeší kontrast
- polohy - blízkost a vzdálenost
- jasu - zvýraznění nějakých prvků
- tvaru - podobnost prvků

A také uspořádání
- vnoření (např. rámečky atd.)
- zarovnání obsahu a prvků

![[Pasted image 20230618174655.png|500]]

Barvy - záleží na kontrastu k ostatním barvám v okolí
- nízký či moc vysoký kontrast - vnímací únava uživatele
- záleží na kultuře, sociální skupině, geografii atd. - každá skupina lidí vnímá určité barvy jinak 

Uživatel se v první řadě zaměří na objekt, který s něčem liší - jiná barva, tučné písmo, rámeček, osamělá poloha atd.
Směr pozornosti uživatele je z horního levého rohu do dolního pravého rohu. Pokud chci, aby se uživatele pozornost směřovala jiným směrem, mohu tomu pomoci nakreslením skutečné linie či organizováním obsahu do toho směru.

### Pojem: Affordances
- vlastnosti, kterými objekt nabízí způsoby manipulace s ním samotným
- založeno dost na předchozí zkušenosti uživatele (už ví, jak se s tím zachází)
- **explicitní** - přímo nám říkají, jak se s tím manipuluje (vyvýšené tlačíko, popis "Click to...")
- **vzory** - podle obecně známé konvence (hamburger button)
- **skryté** - objeví se až po určité akci - mouse over
- **falešné** - červená pro OK
- **metaforická** - idk
- **negativní** - s tímto se nedá manipulovat

### Makety a prototypy
- pro různé skupiny prototypy a makety představují trochu něco jiného
- musí být srozumitelné, bez zbytečných detailů, přizpůsobené cílové skupině 
- dělí se podle účelu:
	- pro [[Logická architektura v UCD|logický návrh]] 
		- hierarchie elementů a vedení pozornosti jsou nejdůležitější
		- řeší se blízkost a uspořádání
		- stačí low-fidelity (jednoduché náčrty)
	- pro vizuální návrh
		- nejdůležitější: estetika, vyváženost, uspokojení
		- správné tvary widgetů jsou důležité
		- už jsou potřeba high-fidelity - většinou už elektronické (např. Figma)
- prototyp - je interaktivní
- maketa - je statická
	- ale tohle rozdělení není ostré, s jedním jde dělat to druhé