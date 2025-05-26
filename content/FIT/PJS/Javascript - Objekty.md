Objekty lze mezi sebou dědit pomocí `.__proto__` =>[[Javascript - OOP, Prototypy]]
```js
const o1 = {a: 1}
const o2 = {b: 2}

o2.__proto__ = o1

// nyni mohu přistupoval na o2.a i o2.b
// ale v o1 mám jen o1.a 
```
- vlastnosti se u objektů dělí na "own" a "inherited"
	- spoustu metod na objektech mají v sobě "own" -> to znamená, že se ta metoda týká pouze vlastních vlastností

Klíč objektu může být jen string a symbol
```js
// k objektu mohu přidávat vlastnosti i takhle:
const obj1 = {a: 1}
obj1.b = 12;
```

Deskriptory - pomocí nich mohu ovlivňovat vlastnosti vlastností objektů.
Informace o konkrétní vlastnosti zjistím takto:
```js
let descriptor = Object.getOwnPropertyDescriptor(obj, propertyName);
```

```js
Object.defineProperty(o, 'a', {
	enumerable: false,
	writeable: true,
	get() {
		return Math.random() // o.a bude vždy dávat jiné random hodnoty
	}
})
```
- get a set vlastnosti vlastnosti jsou dobré na kombinování hodnot jiných vlastností
- lze definovat i více properties najednou pomocí `Object.defineProperties`
```js
Object.defineProperties(user, { 
	name: { value: "John", writable: false }, 
	surname: { value: "Smith", writable: false }, 
	// ... 
});
```

`Object.getPrototypeOf` - dostanu "rodiče" objektu