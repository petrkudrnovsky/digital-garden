Vektorové reprezentace jednotlivých slov pomocí přístupu založeného na strojovém učení
- by Tomáš Mikolov
- založené na principu, že slova s podobným významem leží ve vektorovém prostoru blízko sebe
- místo slov se dá použít i na geny, kód, playlisty apod.

Jedná se o model, který se umí "naučit" sám z velkého množství textů
- používá dva algoritmy/modely: CBOW a Skip-gram
	- CBOW - předpovídá cílové slovo na základě jeho kontextu (aka okolních slov)
		- vstup jsou okolí slova a on předpovídá, jaké slovo je nejpravděpodobnější mezi nimi
	- Skip-gram - opačný přístup, podle konkrétního slova předpovídá okolní slova

Také je možné aritmeticky počítat s jednotlivými vektory
- např vec(král) - vec(muž) + vec(žena) = vec(královna)