> Basically Available, Soft State, Eventually Consistent

Je to alternativa k [[ACID]] modelu v distribuovaných systémech a [[NoSQL databáze]] a [[Big Data]]

Hlavní důraz je tedy na dostupnost a toleranci vůči výpadkům před striktní konzistencí
- viz [[CAP teorém]]
### Součásti
1) Basic Availability (základní dostupnost) - pokud přijde výpadek, systém bude dál dostupný
	- místo chyby vyhodí částečný výsledek či neaktuální data
2) Soft State - stav databáze může být **dočasně** nekonzistentní
	- to způsobuje např. asynchronní replikace nebo výpadky
	- konzistence se obnovuje, ale v delším časovým horizontu (viz bod 3)
3) Eventual Consistency - eventuelně se data sesynchronizují

### BASE vs. [[ACID]]

|**Vlastnost**|**BASE**|**ACID**|
|---|---|---|
|**Dostupnost**|Vysoká, i za cenu nekonzistence dat|Nižší, závisí na udržení konzistence|
|**Konzistence**|Eventual Consistency (časem konzistentní)|Přísná konzistence|
|**Škálovatelnost**|Snadno škálovatelné horizontálně|Obtížnější horizontální škálování|
|**Vhodné aplikace**|Big Data, real-time aplikace|Bankovní systémy, finanční transakce|
|**Odolnost vůči výpadkům**|Toleruje nekonzistenci, udržuje dostupnost|Selhání může narušit konzistenci|
