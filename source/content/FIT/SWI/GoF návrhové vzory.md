= Gang of Four (podle 4 autorů knihy, kde tyto návrhy představili)
Hezky popsáno zde: https://refactoring.guru/design-patterns

- 23 návrhových vzorů ve 3 kategoriích
- nejsou to zákony, spíše ulehčení a doporučení při psaní a navrhování SW
### 1) Creational Patterns
- mechanismy vytváření objektů tak, aby byly vhodné pro danou situaci
##### [Singleton](https://refactoring.guru/design-patterns/singleton) 
- využití: jazykové nastavení, konfigurace, databázové spojení
- anti-pattern: emuluje globální proměnnou
	- proč je to nahovno? špatně se to testuje - singleton se velmi špatně testuje
	- co když mám více databází? více konfigurací?
		- to částečně řeší multiton
- v začátcích se hodí, poté už ne, vyplatí se ho už neuvažovat
##### [Factory](https://refactoring.guru/design-patterns/factory-method)
- místo toho, aby Client vytvářel článek, tak požádá Factory, aby mu ho vytvořila
	- a k tomu článek "zabalím" do Interface, takže s ním můžu pracovat jako s obecným článkem
	- tím pádem mohu mít Article a Comment tvořený různými Factory, ale tyto dva objekty sdílí jeden Interface
- factory vytváří jeden jediný interface, ale ten může implementovat více objektů, které jsou tvořeny jednotlivými implementacemi té factory
##### [Abstract Factory](https://refactoring.guru/design-patterns/abstract-factory) 
- umožňuje vytvářet více příbuzných či závislých objektů bez znalosti implementace jejich konkrétních tříd
	- konkrétní implementace je pak dána použitou továrnou
![[Pasted image 20240106135048.png]]
##### [Builder](https://refactoring.guru/design-patterns/builder) 
- je řízen Director třídou - řídí strukturu výsledného produktu
	- Director ví pořadí operací, Builder ví implementaci konkrétních operací
- Builder umí postavit jednotlivé části produktu
- hodí se pro vytváření komplexních objektů
- třída, která něco sestavuje
- udělám si implementaci menších částí a pak je mohu spojovat a třeba řetězit za sebe
- mohu složitější objekty sestavovat za běhu aplikace
![[Pasted image 20230522120708.png|600]]
##### [Prototyp](https://refactoring.guru/design-patterns/prototype)
- kopírování existujících objektů
- není nutné ruční sestavování (třeba mám i nedostupné `private` properties)
	- nebo znám jenom interface k tomu objektu atd.
	- hlavně, když kopíruju takhle objekt ručním kopírováním, stávám se *závislým* na tom objektu a to nechci
- většinou implementován metodou `clone()` - tedy deleguji proces klonování na ten samotný objekt
### 2) Structural Patterns
- uspořádání tříd či komponent aplikace
- lepší přehlednost
##### [Proxy](https://refactoring.guru/design-patterns/proxy)
- poskytuje alternativní přístup k objektu (pro lazy-loading (načtení celého objektu, až když ho opravdu potřebuji), caching)
- video do paměti nenahraju
	- např. se dotazuju jenom na metadata k videu
	- ProxyVideo má pak odkaz na skutečný objekt s videem - nemusím ho loadovat dokud nemusím
	- implementuji třídu, která má stejný interface jako ta původní
		- a mohu měnit chování před a po volání nějakých metod (stejně jako funguje [[Proxy]])
![[Pasted image 20240106135643.png|400]]
##### [Adapter](https://refactoring.guru/design-patterns/adapter)
- propojuje různé třídy s různým rozhraním (dodržení kompatibility mezi dvěma rozhraními)
- konvertuje rozhraní jedné třídy na rozhraní druhé třídy
##### [Bridge](https://refactoring.guru/design-patterns/bridge) 
- oddělení abstrakce od implementace, aby se mohly vyvíjet samostatně
- abstrakce se zajímá např. o to, jaké GUI komponenty se budou používat, ale o konkrétní implementaci se nezajímá - to pak řeší ona "propojená" část - to se pak dá vyvíjet samostatně a často měnit
- propojuje abstraktní části s jejich různými implementacemi, tedy veškerou logiku propojování mám na jednom místě
##### [Facade](https://refactoring.guru/design-patterns/facade)
- zjednodušuje rozhraní a sdružuje více metod a objektů, aby klienti byli izolováni od složitosti komponent
- klienti pak nepotřebují znát, jak to přesně funguje "under the hood"
- např. mám metodu `createOrder()`, která ve skutečnosti vezme věci z košíku, spočítá je, nacení, uplatní slevy, zavolá platební bránu, vypíše shrnutí objednávky, vygeneruje PDFko, pošle e-mail apod.
##### [Composite](https://refactoring.guru/design-patterns/composite)
- je možné skládat objekty do stromové struktury a pak s touto strukturou pracovat jako s jedním objektem
- př. adresáře, skupiny, menu
- př. objednávka v sobě má položky a balíčky položek (ve kterých mohou být položky i menší balíčky položek)
	- celkový objekt mi může dávat celkovou cenu položek, počet položek apod.
##### [Decorator](https://refactoring.guru/design-patterns/decorator)
- obohacuje funkcionality objektů aniž by měnil jejich skutečnou implementaci
- obalí je do jiiných objektů s přidanou funkcionalitou
	- tímhle způsobem mohu kombinovat více funkcionalit dohromady na základě preferencí uživatele (aniž bych v kódu měl vytvořené všechny možné kombinace - to by bylo neúnosné)
- hodně se používají v [[Python|Pythonu]]
##### [Flyweight](https://refactoring.guru/design-patterns/flyweight)
- podobný jako multiton
- nezabývá se vytvářením objektů
- instance sdílí stejné vlastnosti - šetření pamětí
	- na stránce mám 100x stejné tlačítko, akorát s jiným textem
	- šetření RAM tím, že se sdílí stejné vlastnosti mezi objekty
- např. mám Tree() se souřadnicemi X,Y a zbytek vlastností je v TreeType() - např. název, barva, textura apod. a to sdílí všechny stromy stejného typu
### 3) Behavioral Patterns
- zaměření na komunikaci mezi objekty, jak spolu interagují a jak jsou rozděleny zodpovědnosti
##### [State](https://refactoring.guru/design-patterns/state) 
- odděluje chování třídy závislé na stavu od samotné třídy (v závislosti na stavu třídy se mění její chování)
- odstraňuje složitá větvení - switch, case, if else
- stav není přístupný zvnějšku
	- objekt si drží informaci o stavu a sám si jej upravuje (existují přechody mezi stavy)
![[Pasted image 20230522121036.png|600]]
##### [Observer](https://refactoring.guru/design-patterns/observer) 
- *nevolejte nás, zavoláme vás*
- pozorující objekty (observers) se zaregistrují u pozorovaného objektu (observable)
- pozorující objekt musí implementovat požadované rozhraní
- při změně pozorovaného objektu pozorovaný objekt upozorní všechny jeho zaregistrované pozorující objekty, které na to pak mohou reagovat
- jsou to vlastně Subscribery
##### Command
- akci zapouzdří jako objekt, je možné ho volat kdykoliv
- všechny informace pro tu akci zapouzdří v jednom objektu
- podporuje i "undo" - tedy informace o opačném příkazu
	- můžeme akce dávat jako parametry, dávat je do fronty, či je zpožďovat
##### Visitor
- přidává novou funkcionalitu do hierarchie tříd bez úpravy původních
- přidá operaci, která se má vykonat na elementech struktury, ale nemění třídy elementů
- je to výhodné pro návrh a analýzu, kdy chci provádět různé operace a zároveň nechci "znečišťovat" původní kód
- v podstatě je to nová třída, která má metody, do kterých pošleme původní objekty, na kterých jsme chtěli dělat úpravy, provede je v rámci té metody a vrátí výsledek
##### Template method
- definuje kostru algoritmu v methodě a části kroků deleguje do podtříd/podmetod
- používá se, když nějaké algoritmy mají společnou strukturu a odlišnosti v detailech
	- ty odlišnosti pak implementují jednotlivé podtřídy
	- ale většina společného kódu bude v tom rodičovi
##### Strategy
- objekt definuje konkrétní algoritmus a pro více algoritmů existuje rozhraní, díky kterému se dají jednoduše měnit
	- když je potřeba mít více různých typů algoritmů a dynamicky je měnit v runtime
##### Chain of responsibility
- umožňuje předávat požadavek přes řetězec zpracovatelů, když jeden objekt nedokáže/nechce zpracovat požadavek, předá ho dál v řetězci
- je to vhodné, když máme více zpracovatelů požadavku a dopředu nevíme, který ho bude zpracovávat
- př. [[Autorizace]]/[[Autentizace]], cache, logování...
##### Mediator
- organizuje interakce mezi objekty (zprostředkovává komunikaci mezi nimi)
	- tím redukuje vzájemnou závislost těchto objektů (nejsou závislé na sobě přímo, ale přes Mediatora)
- př. presenter, controller
##### Interpreter
- reprezentace virtuálního jazyka (v rámci nějaké vlastní gramatiky)
- používá se pro časté interpretace malých jazyků nebo jednoduchých skriptů
- např. markdown interpreter, BBcode, definice hodnocení v nějakém známkovacím systému
![[Pasted image 20240106144612.png]]
