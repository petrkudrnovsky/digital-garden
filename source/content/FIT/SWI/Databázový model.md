Problém je, že relační databáze nepodporují dědičnost, kterou jsme si tak hezky vymysleli a nadefinovali v [[Doménový model|doménovém modelu]]. Lze na to jít 3 přístupy:
1) [Single Table Inheritance](https://martinfowler.com/eaaCatalog/singleTableInheritance.html)
2) [Concrete Table Inheritance](https://martinfowler.com/eaaCatalog/concreteTableInheritance.html) - nelze ukládat instance rodiče, pouze potomky
3) [Class Table Inheritance](https://martinfowler.com/eaaCatalog/classTableInheritance.html) - všechny třídy jsou namapované

![[Pasted image 20230521190019.png]]

Je možné, že takový model bude velký, pro přehlednost není na škodu jej rozdělit do více logických balíčků a ty mezi sebou pak referencovat (definovat vztahy mezi jednotlivými tabulkami v různých balíčcích). Při referenci nějaké tabulky z jiného balíčku lze danou tabulku zobrazovat v kompaktním rozložení.

\<\<trace\>\> je mapování entit mezi [[Doménový model|doménovým]] a databázovým modelem
