### Syntaxe
- definice proměnné pomocí const - nemohu měnit, to je jasné, ale pozor, pokud tam uložím objekt, tak už tam nemůžu dát jiný objekt, ale vnitřek objektu měnit mohu

```js
var a; // zde je nyní uložený undefined
var d = c = b; // bad practice, zásah do globálních proměnných
var e = 3, f = 4; // bad practice -> nečitelné
```

- rozdíl parametr a argument
	- parametr je nějaká nadefinovaná proměnná, kterou uvádím při definici funkce
	- argument je samotná hodnota v parametru, která se předává v konkrétním volání

##### Rozdíl `var` X `let`
- `var` má **function** scope, to znamená, že kašle na blok statements (jenom {}, for cyklus, if cykly...) a je vidět všude v rámci funkce bez ohledu na to, kde je definována
- `let` má **block** scope, to znamená, že platí jenom v rámci bloku v kterém je definována (chci používat toto, block scope dává smysl)
- `const` se přiřadí pouze jednou a pak už se nedá změnit (pozor, pokud je přiřazený objekt, tak nemohu přiřadit objekt, ale mohu změnit obsah toho objektu)

```js
console.log(new Error().stack);
```

##### Typeof operátor
```js
let a = 12;
if (typeof a === 'integer') {
	console.log('It\'s integer.');
}
```

##### Práce s čísly
- pro převod ze stringu
	- `Number("string")` - zvládá to i desetinnou tečku/čárku
	- `parseInt("string")` nebo `Number.parseInt("string")` - usekne desetinnou čárku (funkce jsou ekvivaletní)
- `parseInt()` umí i převody do dalších soustav (ze soustavy do desítkové)
	- `console.log(parseInt('a7', 16)) // 167`
	- opačně např. pomocí toString()
		- jedná se o Number.prototype.toString()
		- `(123).toString(16) // '7b', 16-ova soustava`
- ovlivnění počtu desetinných míst (pozor, output je string)
	- `(123.321).toFixed(5) // '123.32100'`
	- `(123.321).toFixed(1) // '123.3'`
ošetření číselného vstupu:
```js
function double(n) {
	// isFinite(NaN) === false
	// Number(n) - pokud se nepovede převést, tak je to NaN
	if(!Number.isFinite(Number(n))) {
		throw new Error('Argument is not a number!');
	}
	return n*2;
}
```

##### Práce se stringy
Metody, které se dají zavolat na `String.prototype.metoda` se dají zavolat přímo na nějaké instanci
- `"Ahoj".trim()` atd.
Metody, které jsou statické (`String.fromCharCode()`) se dají volat jen na `String`

`"3" > "29999"` - stringy se porovnávají lexikograficky

##### Catch u Booleanu
```js
let a = Boolean(false); // primitivum
let b = new Boolean(false); // objekt, který v sobě obsahuje hodnotu false, ale objekty jsou truthy by default
```
