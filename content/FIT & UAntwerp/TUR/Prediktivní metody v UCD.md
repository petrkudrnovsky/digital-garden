Používáme většinou na začátku, a když chceme rychlé vyhodnocení použitelnosti. Máme většinou první prototyp a ještě nemáme reálné uživatele - ty budeme simulovat "hrajícími" HCI experty.
- HCI experti využívají své znalosti HCI + chovají se podle [[Persony v UCD|person]] + "předpokládají" že prototyp je reálný artefakt
- tedy jde o metody založené na simulaci uživatele
- jejich cílem je kontrola a predikce kroků (budoucího) uživatele

Hlavním výstupem jsou potenciální problémy v použitelnosti artefaktu.

#### Prediktivní metoda: Inspekce
- stránka po stránce, okno po okně, dialog po dialogu
- vyjde nám heuristické vyhodnocení
	- kontrolujeme všechny části artefaktu vůči nějakému seznamu heuristických pravidel (přepřipravené otázky)
		- například heuristická pravidla od [Nielsena](https://www.nngroup.com/articles/ten-usability-heuristics/) 
- dále se provádí inspekce různých standardů (firemní, normy, zákony, [[HIGs a UX]])
- inspekce konzistentnosti (taky pomáhají [[HIGs a UX]])
- jestli artefakt obsahuje všechy features
	- a jestli jich naopak nemá moc - vede k "creeping featurism"

#### Prediktivní metoda: Průchod
- otázka - splňuje artefakt daný uživatelský cíl?
	- prochází se sekvence akcí, která k danému cíli směřuje
![[Pasted image 20230615185908.png|600]]
- pro každý průchod vytvořím Model uživatele (GOMS)
	- Goal - cíl uživatele
		- musí dávat smysl i bez artefaktu
		- musí vyžadovat nějakou posloupnost akcí s artefaktem
		- musí být konkrétní (data, názvy apod.)
	- Operators - atomické činnosti, které se dají dělat s artefaktem
	- Methods - posloupnosti Operators, vedou k nějakému cíli
	- Selection rules - kterým Operátorům či Metodám se dává přednost (z uživatelského hlediska)
		- nějaké mohou být ve větší preferenci

##### Kognitivní průchod
- máme uživatelský cíl a posloupnost akcí, která k němu vede - otázka je, jak je pravděpodobné, že uživatel tudy půjde? Ví, že má tudy jít, je to jasné? Opravdu tuto možnost zvolí? Rozumí odezvě systému?
- co potřebujeme?
![[Pasted image 20230615190349.png|600]]
- uživatele nám tady může simulovat nějaký HCI expert, který se chová podle persony (máme z průzkumu)
- posloupnost akcí, jak si je představují návrháři
- provedení:
	- vezmu každou akci posloupnosti a zkoumám, jestli je věrohodné, že ji uživatel takhle provede
		- pokud ne, poznamenat proč a pokračovat dál
- výstup: seznam problémových míst

##### Pluralistický průchod
- kombinuje Kognitivní průchod a [[Metody a techniky práce s uživateli#4) Kreativní skupiny|kreativní skupinové techniky]]
- máme 2 skupiny: uživatele a návrháře, kteří si mezi sebou vyměňují informace
	- výměna je moderována moderátorem (jen pro efektivitu)
- v diskusi mají uživatelé přednost (jsou primární)
- průběh:
	1) vysvětlí se aktuální cíl uživatele, který zkoumáme
	2) každý účástník zaznamená jeho průchod aplikací k cíli
	3) diskutují průběh
	4) vysvětlení správného průběhu moderátorem
	5) vyplnění krátkého dotazníku

##### Heuristický průchod
- hybrid mezi inspekcí a průchodem
- první průchod je zaměřený na podstatné části artefaktu
	- zaznamenávají se otázky k soustředění (z kognitivního průchodu)
- druhý průchod je kontrola cest vzhledem k heuristickým kritériím
- nejčastěji se metody takhle kombinují