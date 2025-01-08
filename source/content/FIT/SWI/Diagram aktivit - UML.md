![[Activity_Diagram.pdf]]

![[Pasted image 20230519165900.png]]
![[Pasted image 20230519165907.png]]
- Počáteční uzel - generuje ==tokeny== - simuluje průchod procesem
- Každá akce se spustí až tehdy, když má na všech vstupech token
![[Pasted image 20230519170236.png]]
- zelený kosočtverec - pokud mu na vstup přijde jakkýkoliv token, pošle ho dál
- hnědá ploška - nepustí tokeny dál, dokud nebudou na vstupech všechny (taková Bariéra v [[BI-OSY - rozcestník|BI-OSY]]) - dále pokračuje jen jeden
![[Pasted image 20230519170513.png]]
- zde také vidím znázornění různých zodpovědností aktérů za části procesu (znázorněno v tzv. *swimlines*)

![[Pasted image 20230519170434.png]]
![[Pasted image 20230519170544.png]]
- Přidání objektového toku
	- objektový a řídící tok na sobě vůbec nezávisí a nekolidují
- Doporučení: začít bez objektových uzlů a pak je tam postupně přidat
- Pro zachycení předávaných dat/entit mezi akcemi (lze doplnit i informaci o stavu)

![[Pasted image 20230519170842.png]]
- pokud token projde "bleskem", tak se zruší všechny akce vykonané v tom regionu


### Chyby v diagramu aktivit


![[Pasted image 20230519171037.png]]

![[Pasted image 20230519171049.png]]
- z chyb si beru také to, že z akce vychází jen jedna šipka, veškeré rozdělování zajišťují kosočtverce a plošky
	- do akce může vcházet více tokenů, ale samotná akce se spustí až tehdy, když jsou na všech vstupech tokeny