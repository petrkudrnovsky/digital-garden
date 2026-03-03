- definování / přepisování metod objektů
- zde `String` má object `prototype` a na něm jsou metody
```js
String.prototype.reverse = function() {
	return this.split('').reverse().join('');
}
console.log("ahoj".reverse());
// toto je bad-practice - může se stát, že to neošetří všechny případy a už se to chová jinak než by programátor očekával

// jediná možnost, kdy se to dá využít "legálně" tak je, když už používám novou funkci JS, která ještě není dostupná ve starších prohlížečích, tedy definuji onu funkci nebo nějakou jinou, která její chování nahradí
// tato technika se jmenuje polyfillování metod
String.prototype.reverse = String.prototype.reverse || function () {...}
```

- polyfill metod: https://developer.mozilla.org/en-US/docs/Glossary/Polyfill
- mými slovy, polyfill je kus kódu nebo plugin, který zajišťuje funkcionality, které, já jako vývojář předpokládám, že browser umí natively
	- problém je, že existují starší prohlížeče, kde nejsou podporované funkce, které používám, tak pokud nejsou dostupné, tak nadefinuji funkci, která splňuje stejnou funkcionalitu, kterou od toho očekávám (viz kód výše)
	- https://remysharp.com/2010/10/08/what-is-a-polyfill - slovy tvůrce tohoto termu

- monkey patching můžu použít také k tomu, abych nepřepsal původní metodu, ale jenom ji doplnil
```js
MathObject.add = function(a, b) {
	// add code
	return MathObject.add; // "stará" funkce
}
```