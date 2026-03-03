Osobní content na webu nyní vidím čím dál více, obsah generování mně přímo na míru, osobní profily, komunity atd.

Problém při u sociální sítí je velký **information overload** a **interaction overload** a content-based vyhledávání už nebylo dostačující.

Doporučovací metody obecně:
- vezmou uživatele a jeho historii + aktuální context (webová stránka, lokalita, čas, období, aktuální zobrazený produkt)
- a vyhodí top-k produktů podle ratingu
	- prezentace a doporučení top-k nejlepších produktů
- sbírají uživatelskou zpětnou vazbu, učí se preference a pak na základě toho doporučují

**Uživatelská zpětná vazba** je 
1) explicitní - například hodnocení hvězdičkami
	- je to jako anotace - přesně daná preference od uživatele
	- nevýhoda - takový feedback není zaručený, není komplentí, subjektivní, nekonzistentní v čase apod.
2) implicitní - podle chování uživatele
	- těžko se to interpretuje
	- je to zdarma a objektivní (nedá se to nafejkovat)

### Přístup č. 1 - Kolaborativní filtrování
- na základě shody v preferencích různých uživatelů
- spočítají se podobnosti mezi uživateli, vybere se podmnožina nejpodobnějších aktivnímu uživateli a vypočítá se předpokládaný rating aktivního uživatele (na základě ratingů ostatních uživatelů)
![[Pasted image 20230528192003.png|400]]
- cosinová vzdálenost, Spearmanův či Pearsonův koeficient
- může být **user-based** nebo **item-based**
	- u item-based počítám predikci pro konkrétní produkt na základě uživatelova hodnocení podobných produktů
- problémy
	- uživatelé mají často jen jednu session - je potřeba se učit rychle
	- obtížné rozeznat kontroverzní a populární produkt
	- výpočet podobnosti uživatelů je výpočetně náročný

### Přístup č. 2 - Content-based doporučování
- místo hodnocení user-item chci informace (content deskriptory) itemů a uživatelů 
![[Pasted image 20230528192426.png|400]]
- zase hledám k nejbližších sousedů
- k vyhodnocení podobnosti na základě obsahu mohu použít [[Vektorový vyhledávací model]] nebo další (podle multimédií)
- výhoda
	- výběr nezávisí na ostatních uživatelích
	- transparentní - dává to smysl
	- nové itemy mohou být jednoduše začleněny - ne cold-start u Kolaborativního filtrování
- nevýhody
	- nějaký content se těžko extrahuje a porovnává (multimédia)
	- přespecializace - když existuje moc podobných produktů
	- přijde nový uživatel - nejdřív si o něm systém musí něco dozvědět

### Přístup č. 3 - Knowledge-based doporučování
- kde nestačí kolaborativní filtrování a content-based doporučování
- pro produkty, které se nekupují tak často, nemají tolik hodnocení
	- drahé či profesionální produkty

### Přístup č. 4 - Social information retrieval
- na sociálních sítích, se zaměřením na skupiny lidí
- tzv. Social PageRank - podobné [[Page Rank a HITS]], ale pro sociální sítě (např. tweeter je populární a důvěryhodný, když je retweetovaný populárními tweetery)
- relevantní uživatelé mohou být omezení na množinu "přátel"
	- pak se dají použít techniky kolaborativního filtrování a content-based doporučování
	- do hry přichází nový atribut, podle kterého se dá re-rankovat - jak se to líbí přátelům
	- globální a lokální trendy