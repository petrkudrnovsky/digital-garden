Snaha OOP (= Objektově Orientované Programování) je snaha modelovat principy reálného světa při řešení (nejlépe) jedna ku jedné. OOP vede ke zrychlení vývoje a přehlednosti programů - je možné snadno znovupoužívat již vytvořené a odladěné části.

Základem OOP je objekt, ten má atributy a metody. Objekt (= instance třídy) má typ (= třídu).

Při řešení problému jej rozložíme na nové datové typy (= třídy), specifikujeme jejich data a operace, které budou provádět a jak budou spolupracovat.
### 4 základní vlastnosti OOP
1) **abstrakce** - definování společných vlastností a metod bez nepotřebných detailů, je možné vytvářet abstraktní třídy a rozhraní, které definují chování jejich potomků, abstraktní třídy nelze instanciovat přímo (protože nemají definované metody), každý z potomků pak implementuje svoji logiku sám
	- zkrátka z části kódu uděláme "černou skříňku", která poskytuje nějaké metody (ale programátor nezná jejich přesnou implementaci)
2) **zapouzdření** - spojování dat a logiky do větších logických celků - objektů a rozhodování o přístupnosti k datům a metodám zvenčí (private, protected, public), můžeme zvenčí povolit přístup pouze k určitým metodám a vnitřní logiku nechat nedotčenou
	- implementační detaily jsou schovány za množinou veřejných metod
3) **polymorfismus** - objekty se mohou chovat jinak v závislosti na jejich kontextu, tedy pokud dědí z nějaké abstraktní třídy, která pro ně definuje abstraktní metody (které si každý objekt definuje sám, podle své logiky), tak mohu pak volat dané metody na abstraktní třídě a vykoná se logika konkrétního potomka
	- method overriding a overloading
	- při volání metody je implementace metody určena typem objektu a ne typem proměnné
		- v ukazateli na nadtřídu se může ve skutečnosti nacházet její potomek
	- skutečná metoda, která se bude volat se určí až dynamicky za běhu
	- v C++ jsou to virtuální metody
4) **dědičnost** - hierarchičnost tříd, tento princip dovoluje objektům, které dědí z nějakého jiného objektu "zdědit" jeho metody a atributy, tedy pokud jsou nějaké společné pro více objektů, tak tím dosáhneme přehlednosti, dodržím DRY principle a máme možnost měnit logiku na jednom místě
	- třída může dědit z více tříd - ale ne u všech jazyků
	- podtřídy mění nadtřídy či jim přidávají konkrétnější schopnosti
		- metody nadtřídy mohou být překryty metodami podtříd (overwrite)
### Další pojmy OOP
- skládání - objekty je možné do sebe vzájemně skládat (atributy jednoho objektu jsou také objekty)
- delegování - objekt využívá metod a služeb jiného objektu
- abstraktní metoda - má pouze deklaraci
	- konkrétní definice metody je pak definovaná v potomkovi
- abstraktní třída - třída, která má min. 1 abstraktní metodu
	- nemůže mít instanci, pouze její potomci, kteří už implementují konkrétní abstraktní metody
- přetížení metod (overload) - v jedné třídě je více metod stejného jména, ale s jinými parametry (jiný počet, jiné typy) 
	- při kompilaci se rozhodne (podle parametrů), která metoda se bude volat
	- při výběru se neuvažuje návratový typ metody