### 4 základní vlastnosti OOP
1) **abstrakce** - definování společných vlastností a metod bez nepotřebných detailů, je možné vytvářet abstraktní třídy a rozhraní, které definují chování jejich potomků, abstraktní třídy nelze instanciovat přímo (protože nemají deklarované metody), každý z potomků pak implementuje svoji logiku sám
2) **zapouzdření** - spojování dat a logiky do větších logických celků - objektů a rozhodování o přístupnosti k datům a metodám zvenčí (private, protected, public), můžeme zvenčí povolit přístup pouze k určitým metodám a vnitřní logiku nechat nedotčenou
	- implementační detaily jsou schovány za množinou veřejných metod
3) **polymorfismus** - objekty se mohou chovat jinak v závislosti na jejich kontextu, tedy pokud dědí z nějaké abstraktní třídy, která pro ně definuje abstraktní metody (které si každý objekt deklaruje sám, podle své logiky), tak mohu pak volat dané metody na abstraktní třídě a vykoná se logika konkrétního potomka
	- method overriding a overloading
	- při volání metody je implementace metody určena typem objektu a ne typem proměnné
1) **dědičnost** - hierarchičnost tříd, tento princip dovoluje objektům, které dědí z nějakého jiného objektu "zdědit" jeho metody a properties, tedy pokud jsou nějaké společné pro více objektů, tak tím dosáhneme přehlednosti, dodržím DRY principle a máme možnost měnit logiku na jednom místě
	- třída může dědit z více tříd - ale ne u všech jazyků