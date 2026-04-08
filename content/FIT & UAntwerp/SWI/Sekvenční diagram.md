Diagram ukazuje komunikaci mezi dvěma účastníky - instancí třídy B (anonymní) a objektem A třídy A.

```mermaid
sequenceDiagram
    participant B as :Třída B
    participant A as Objekt A:Třída A

    B ->> A: asynch. zpráva()
    B ->>+ A: synch. zpráva()
    A -->>- B: 
```

Jsou zde znázorněny dva typy zasílání zpráv:
- asynchronní zpráva - odesílatel nečeká na odpověď a pokračuje dál
- synchronní zpráva - odesílatel čeká na návrat řízení zpět (znázorněno přerušovanou šipkou zpět)
- :Třída B je nepojmenovaný objekt, Objekt A:Třída A má jméno Objekt A
	- bez dvojtečky by to byla statická třída
# Návratová hodnota
Existují 2 různé způsoby, jak znázornit návratovou hodnotu v sekvenčním diagramu.

```mermaid
sequenceDiagram
    participant A as :Třída A
    participant B as :Třída B

    A->>B: vysledek= zprava()
    A->>B: zprava()
    B-->>A: vysledek
```

- První způsob zachytí návratovou hodnotu přímo v zápisu volání zprávy ve formátu `vysledek= zprava()`.
- Druhý způsob odešle zprávu `zprava()` a návratová hodnota `vysledek` je vrácena samostatnou přerušovanou šipkou zpět volajícímu.
	- přehlednější je ten druhý způsob
# Vytvoření a zrušení objektu
Sekvenční diagram ukazuje dva klíčové jevy v životním cyklu objektu:
- Vytvoření objektu - znázorněno stereotypem `«create»`, kdy instance Třídy A dynamicky vytvoří nový objekt Třídy B během běhu programu
- Zrušení objektu - znázorněno stereotypem `«destroy»`, po němž životní linie objektu končí symbolem ×

```mermaid
sequenceDiagram
    participant A as :Třída A
    participant B as Nový objekt:Třída B

    A->>+B: «create»
    A->>B: zpráva()
    A->>B: «destroy»
    note over B: ×
```
# Sekvenční diagram - speciální typy zpráv

Slide popisuje dva speciální koncepty sekvenčního diagramu:
- Nalezená zpráva (Found Message/Endpoint) - zpráva, jejíž odesílatel není v diagramu znám, znázorňuje se plným kruhem na začátku šipky
- Zaslání zprávy sám sobě (Self Message) - objekt volá vlastní metodu, šipka se vrací zpět na stejný životní pruh

Příklad ukazuje třídu TřídaB, kde `metodaB1()` interně volá `this.metodaB2()` a uloží výsledek - jde tedy o Self Message.

```mermaid
sequenceDiagram
    participant B as :TřídaB
    note over B: Found Message (zdroj neznámý)
    B->>B: metodaB1()
    activate B
    B->>B: vysledek = metodaB2()
    deactivate B
```

Odpovídající kód třídy:
```java
class TřídaB {
  public void metodaB1() {
    vysledek = this.metodaB2();
  }
  public int metodaB2() {...}
}
```
- "něco" zavolalo metoduB1 - ukazuje se reakce :Třídy B
- volání metody sám na sobě
# Sekvenční diagram - fragmenty
Fragment umožňuje zachytit podmíněné chování a opakování v sekvenčním diagramu. Rozlišujeme tyto druhy fragmentů:
- větvení (alt) - podmíněné větvení podle hodnoty podmínky
- cyklus (loop) - opakování zprávy dokud platí podmínka
- další typy jako opt (volitelné) a par (paralelní)

Iterace přes prvky kolekce se zapisuje jako loop fragment s podmínkou ve tvaru for cyklu, např. `for i=0; i<size(objekty); i++`, přičemž zpráva se odesílá na konkrétní prvek kolekce označený indexem.

```mermaid
sequenceDiagram
    participant B as :Třída B
    participant A as Objekt A:Třída A
    participant C as Objekty[i] : Třída C

    alt [a>b]
        B->>A: zpráva A()
    else [a<=b]
        B->>A: zpráva B()
    end

    loop [a<b]
        B->>A: zpráva A()
    end

    loop iterace přes prvky kolekce [for i=0, i<size, i++]
        B->>C: zpráva C()
    end
```
- pro větvení se používá ALT, pokud je tam i else větev, pokud je tam jenom if, tak mohu použít OPT
# Ukázka: sekvenční diagram VypujceniVytisku
Diagram zachycuje průběh výpůjčky výtisku mezi objekty systému.

```mermaid
sequenceDiagram
    participant vytisk as vytisk :VytiskDTO
    participant stav as :StavVolny
    participant kniha as knihaVypujcek :KnihaVypujcek
    participant vypujcka as vypujcka

    vytisk->>stav: lZpracuj(Ctenar, VytiskDTO)
    stav->>kniha: zapisVypujcku(VytiskDTO, Ctenar)
    kniha->>vypujcka: new()
    kniha->>vypujcka: setVytisk(VytiskDTO)
    kniha->>vypujcka: setCtenar(Ctenar)
    kniha->>vypujcka: setVypujcenaDo(Date)
    Note over vypujcka: objekt zaniká (×)
    kniha-->>stav: návrat
    stav-->>vytisk: setStav(new StavVypujceny())
```

Průběh scénáře:
- Objekt `vytisk` volá na stavu metodu `lZpracuj` s parametry čtenáře a výtisku
- Stav zavolá na knize výpůjček metodu `zapisVypujcku`
- Kniha vytvoří nový objekt `vypujcka` a nastaví mu výtisk, čtenáře a datum vypůjčení do
- Po dokončení se vrátí řízení zpět a stav výtisku se změní na `StavVypujceny`
