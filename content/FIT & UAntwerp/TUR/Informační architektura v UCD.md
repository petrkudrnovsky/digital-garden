Je to způsob, jak si uživatel a artefakt vyměňují informace. Tyto způsoby (jak je navrhneme), tak plynou hlavně z cílů uživatelů a závisí na kvantitativních okolnostech (tj. délka tabulky, počet sloupců, počet položek v seznamu). Řeším, v jaké formě či v jakých vztazích jsou informace předávány uživateli. Definuje, jak se celek bude skládat z částí (stránek, oken) a jaké budou jejich úlohy.

Zahrnuje způsoby prezentace dat, navigační styly a styly interakce.

Abych mohl strukturovaně konstruovat informační architekturu, tak možnosti artefaktu dekomponuji na aktivity (snažím se, co nejmenší - granularita - pak z malých aktivit skládám delší sekvence, lépe se s tím pracuje) a hledám společné aktivity pro více cílů (ty pak mohu sloučit). Zároveň navrhnu sekvence pro všechny cíle uživatele.
- standardní postupy najdeme v [[HIGs a UX]] - Human Interface Guidelines

### Styly interakce
1) Text (přirozený jazyk, příkazové řádky) - uživatel si musí rozpomínat, nic mu nenapovídá
	1) zachovává se průběh dialogu, ne jeho stav
2) Přímá manipulace - zobrazeno je vše co dává z hlediska uživatele smysl, co má podobu v reálu, s čím se dá manipulovat
	1) problém: uživatel nemusí vědět, s čím vším (a jak) se dá manipulovat

### Stav systému
- ovlivňuje reakci systému na vstupy od uživatele
- otázka do jaké míry chceme uživateli ukazovat, v jakém je systém stavu (abychom ho nepřetížili, ale zároveň, aby měl přehled a nemusel si všechno pamatovat)
![[Pasted image 20230618171707.png|500]]

### Jak prezentovat
- návrh informační architektury prezentujeme kolegům, uživatelům a zákazníkům, aby to mohli posoudit, vyhodnotit, vylepšit/předělat -> pak se to zahodí, je to jenom pracovní materiál
- hlavní výstup je Graf přechodů

### Graf přechodů
- máme atomické aktivity, pospojované do sekvencí a tyhle sekvence jsou sloučené do grafu přechodů, což je interakční diagram, který je
	- přehledný (ukazuje více cílů najednou)
	- vhodný ke znázornění společných kroků
		- jaké kroky můžeme sloučit?
	- není tak názorný - musíme si akce představit

Když děláme informační architekturu, tak ještě nemáme artefakt k dispozici (logicky), tak musíme prezentovat jinak => Storyboardingem
- mám k dispozici všechno z formativní fáze, sekvence výměny informací uživatel X (budoucí) artefakt a princip možných akcí
- tento způsob pochází od Walta Disneyho a je to efektivní způsob komunikace informační architektury (co a jak se bude provádět, kde je problém, jak ano, jak ne)
	- z uživatelských příběhů (v [[Persony v UCD|personách]]) složím sdělení, hodím tam pár klíčových okamžiků ze sekvencí, nakreslím to a mám vyhráno