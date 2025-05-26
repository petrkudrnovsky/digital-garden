Hoisting - mechanismus, který přesouvá deklarace proměnných a funkcí na začátek scope, tedy ještě před tím, než jsou fyzicky definovány. Dokud nemají přiřazenou hodnotu, tak mají hodnotu undefined.
```js
function something() {
	a = 6;
	console.log(a);
	var a;
} // vrati "6"
```
### Pojem: IIFE - immediately invoked function execution
- funkce, která se zavolá ihned po svém vytvoření
- klasický syntax:
```js
(function()
 { 
	 //... 
 })();
```
-  arrow funkce:
```js
const x = (a) => {
	return a * a;
}

// jeste kratsi
const x = (a) => a * a
const x = a => a * a 
```
- narozdíl od klasické funkce namá name, nemá svůj kontext `this`, nemůžu accessnout pole argumentů pomocí `arguments[x]` 

Zabalení funkce a vnitřního statu funkce, proměnnou i si to drží mezi jednotlivými volání funkce
```js
function createCounter() {
	let i = 0;
	function counter() {
		return i++;	
	}
	return counter;
}

const cou = createCounter(); // zavola se a vrati se counter funkce

console.log(cou())
console.log(cou())
```
