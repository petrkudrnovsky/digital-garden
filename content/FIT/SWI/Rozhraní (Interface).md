- rozhraní - odděluje specifikaci od implementace
	- snižuje provázanost ([[GRASP - Nízká provázanost]])
![[Pasted image 20230522114329.png]]
- Komponenta A nabízí rozhraní (nějakou funkcionalitu)
- Komponenta B potřebuje rozhraní

![[Pasted image 20230522114511.png|Implementace rozhraní]]

![[Pasted image 20230522114547.png]]

Komponenty (třídy) mohu mezi sebou propojovat
- manuálně ve zdrojovém kódu
- pomocí IoC (= Inversion of Control)
	- řeší problém toho, že si třídy samy vytváří/načítají závislosti a to pak je problém při měnění závislostí či odděleném testování
	- "otočení" kontroly tak, že místo toho komponentám dané závislosti dodáme zvenčí (prostřednictvím konstruktoru či nějaké speciální metody)
	- protože jsou závislosti dodány zvenku, tak je můžeme snadno měnit a neovlivníme tím samotnou třídu
	- je to výhoda i u testování, kdy závislosti můžeme nahradit tzv. mocky a simulovat jejich chování
	- s tímto je hodně spojené také DI = dependency injection
		- závislosti jsou injektovány přímo do objektu
		- constructor-based a setter-based