Návrhový vzor obecně řeší nějaký problém, který přede mnou řešilo už více lidí. Jedná se tak často o nejlepší způsob jak danou věc vyřešit = jsou standardizované + každý dobrý programátor jim rozumí, takže se orientuje v mém kódu.

Správný návrhový vzor musí mít jméno, musí za definovaných podmínek řešit nějaký problém, měl by být dostatečně obecný, jasný a srozumitelný.
### [[GoF návrhové vzory]]

#### Front Controller
- řeší běh aplikace, spouští ji
- u mě to bude `index.php`
	- pak to nedělá sám, to rozdělování dělá např. `Router.php`, ale point is, že sídlí v `index.php`
#### Template View
- šablony, dynamické stránky
- nahrazuje definované zástupné symboly, podmínky, cykly apod. v šablonách
- např. [Twig u Symfony](https://twig.symfony.com/) 
#### Inversion of Control (IoC)
- principem, co řeší je, že aby si komponenty nebo třídy vytvářely nebo hledaly závislosti, jsou jim tyto závislosti dodány zvnějšku
	- to snižuje vzájemnou závislost a pevnost vazeb mezi komponentami
- tím se obrací závislosti a lépe se dá kontrolovat tok programu
- **Dependency Injection** - forma IoC
	- závislosti jsou "injektovány" do objektu přes setter nebo konstruktor
- **Service Locator**
	- objekty používají Service Locator k nalezení svých závislostí
	- zná všechny Services a dokáže je vrátit, ale není zodpovědná za jejich skládání - to dělá Assembler