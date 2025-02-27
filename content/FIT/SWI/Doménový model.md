![[Class_Diagram_Domain_Model.pdf]]

### Co je cílem doménového modelu?
- popsat data, se kterými se bude pracovat
- popis významů termínů - aby jim každý rozumněl  
- popis vazeb mezi entitami - pak je to základ pro databázový model (i když ten už se většinou generuje z hotové databáze)
- zachycení atributů jednotlivých entit

Vztah mezi entitami by měl mít vždycky popis:
![[Pasted image 20230520221158.png]]
- kompozice = těsný vztah (jedna entita nemůže existovat bez druhé) - používá se spíše u abstraktnějších věcí než u fyzických
	- s kompozicí opatrně, je hodně striktní
- agregace = něco mezi asociací a kompozicí (radí se moc nepoužívat)
- co se týče dědičnosti, tak je to dost striktní vazba, takže s ní opatrně
	- mrkni, jestli se spíše nedá vyjádřit asociací
		- například: mám Vedoucího tábora a Hlavního vedoucího tábora (místo toho, abych dědil, tak přidám vazbu na Tábor "je vedoucím" a druhou vazbu "je hlavním vedoucím" - tím pádem nedojde k duplikaci)
	- při udělování dědičnosti řeš možné duplikace a kdy mohou nastat (s tím jsem měl problém v testu)

![[Pasted image 20230520221326.png]]
- váže se k asociaci a přidává další atributy (např. délka trvání vztahu)

Třídy (entity) jsou předměty, objekty z reálného světa, či podstatná jména z [[Modelování obchodních procesů|business modelů]], [[Modelování případů užití (Use Cases)|UC modelu]] či slovníčku pojmů (= glosáře, viz TUR)

### Násobnosti
0..1
0..*
1..1 nebo jenom 1
1..*

### Modelování stavů entit
- pro názornost (a přehlednost) lze pro nějaké entity ještě namodelovat stavy
![[Pasted image 20230520223334.png]]
- u výstupních hran se uvádí událost kvůli které se entita převedla do jiného stavu