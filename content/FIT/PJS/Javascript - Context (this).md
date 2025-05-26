- ve 'sloppy' režimu je `this` globální objekt (např. window)
```js
function x() {
	console.log(this);
}
x(); // dostanu globální objekt - window
```
- ve 'strict' režimu je undefined
```js
'use strict'
function x() {
  console.log(this);
}
x(); // undefined
```
- když se volá (dot-notací) na nějakém objektu, tak 'this' referencuje na daný objekt, na kterým je volaná
	- ale pokud metodu (funkci) objektu přiřadím do jiné proměnné, tak 'this' informaci o objektu ztrácí a je buď undefined nebo global (window)
```js
...
const f2 = obj.func;
obj.func(); // vola funkci v kontextu objektu obj a případné this tam referencuje na obj
f2(); // už volá metodu jako klasickou funkci a this referencuje globální scope
```
- [[Javascript - Funkce#Pojem: IIFE - immediately invoked function execution|arrow]] funkce nemá svůj context (vždycky inherituje)
- **this** - jakási vlastnost funkce ("nultý argument") 

### Call vs. apply
- chovají se úplně stejně, ale liší se svojí definicí (zavolají funkci s předdefinovaným contextem)
- call - má jako první argument context, pak to má X dalších argumentů, které jsou pak argumenty té funkce, kterou volám
- apply - jen 2 argumenty - context, array argumentů pro tu funkci, kterou volám

### Bind
- když někam přiřadím funkci, tak ztrácím její 'this', to se dá napravit tím, že jí bindnu její context "natvrdo"
```js
let user = {
	firstName: "John",
	sayHi() {
		alert(`Hello, ${this.firstName}!`);	
	}
}
let sayHi = user.sayHi.bind(user);
sayHi(); // Hello, John!
setTimeout(sayHi, 1000); // Hello, John!
```