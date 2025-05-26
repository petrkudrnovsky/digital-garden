## [[OOP]]
## [[Javascript - OOP, Prototypy#Classes|Skip to Classes]]

## \[\[Prototype\]\]
- klíčové slovo: `__proto__` 
	- vlastnost konkrétní instance
- objekt má svoji hidden vlastnost Prototype, která je buď `null` nebo referencuje jiný objekt (který nastavím) 
- pokud na objektu volám nějaké metody nebo properties a ony chybí, JS se automaticky dívá na Prototype, jestli tam nejsou 

```js
const o1 = { a: 1 }
const o2 = { b: 3 }

o2.__proto__ = o1 // sets o2.[[Prototype]] = o1
console.log(o2.a) // inherited
console.log(o2.b) // own
```
```js
// lze také nastavit takto
let animal = {
	eats: true,
	walk() {
		alert("Animal walk!");
	}
};

let rabbit = {
	jumps: true,
	__proto__: animal
};
```
- řetězec \[\[Prototype\]\] může být delší, ale nesmí jít do kruhu
- `__proto__` se už moc nepoužívá (je to getter/setter na \[\[Prototype\]\])
	- spíš se používá `Object.getPrototypeOf/Object.setPrototypeOf`
- [[Javascript - Context (this)]] - není vůbec ovlivněn prototypy
	- context je jednoduše toho objektu, který je před "tečkou" při volání metody a je jedno jestli je to ten daný objekt nebo jeho prototyp
	- takže v případě setterů na prototypu, pokud je volám "z potomka", tak ovlivňuji state jenom toho potomka, ne toho prototypu

```js
Rabbit.prototype = animal; // když se vytvoří nový objekt Rabbit, přiřaď mu prototype animal
```
- každá funkce má svůj defaultní prototype, ten je přiřazený sám sobě viz: `F.prototype = { constructor: F}` 
	- `F.prototype` nastaví \[\[Prototype\]\] **nového** objektu, když se zavolá `new F()` 

```js
// komentare jsou, co vsechno to udela, kdyz zavolam 'new User(...)'
function User(name, age) {
	// this = {}
	// this.constructor = User
	// this.[[class]] = User
	// this.__proto__ = User.prototype
	this.name = name
	this.age = age
	// return this
}

User.prototype = {
	// zdedena vlastnost
	sayName: function() {
		console.log(this.name)
	}
}

const User = new User('Olda', 28)
```

```js
function Human(name, age) {
	this.name = name
	this.age = age
}

Human.prototype = {
	sayName: function() {
		console.log(this.name)
	}
}

function FPlayer(name, age, team) {
	// toto je dedeni own vlastnosti
	Human.call(this, name, age) // zavolej funkci Human s contextem FPlayera, takze v Human() bude this context context tohoto FPlayer
	this.team = team
}

FPlayer.prototype = {
	sayTeam: function() {
		console.log(this.team)
	}
}

// toto je dedeni prototypu

// Object.create() vytvori novou (prazdnou instanci) s prototypem Human.prototype a vrátí ji 
// tu "prázdnou" instanci s prototypem Humana si přiřadím k FPlayeru a pak už ji mohu modifikovat 
// - kdybych modifikoval přímo, tak budu přidávat sayTeam i k Humanům, kteří nejsou FPlayer a to nechci
FPlayer.prototype = Object.create(Human.prototype)
FPlayer.prototype.sayTeam = function() {
	console.log(this.team)
}
```

Objekty jako klasické objekty, Array, Date, Function mají své **native** prototypes, které se vytvoří a přiřadí automaticky, jakmile je vytvořím (pomocí new Object(), new Array() atd.) - ty metody nemusím přímo volat, volají se i interně (například, když vytvořím \[1, 2, 3\], nebo {})

#### Object.create()
- vytváření objektů bez řešení `__proto__`
```js
let animal = {
	eats: true
};
let rabbit = Object.create(animal); // stejné jako rabbit.__proto__ = animal
console.log(Object.getPrototypeOf(rabbit) === animal); // true
Object.setPrototypeOf(rabbit, {}); // změnil jsem mu prototype na {}

// stejně tak mohu při Object.create() rovnou přiřadit properties navíc
let donkey = Object.create(animal, {
	jumps: {
		value: false
	}
});
alert(rabbit.jumps); // false
```
Pomocí Object.create se dá také efektivně **kopírovat**:
```js
// vytvorime (shallow) kopii objektu obj
let clone = Object.create(
	Object.getPrototypeOf(obj), Object.getOwnPropertyDescriptors(obj)
)
```

## Classes
= šablona pro vytváření objektů, má member variables a member functions = methods

#### Řešení pomocí moderního JS
Třída je vlastně taková převlečená funkce, ale není to jenom syntaktický sugar
```js 
class Human {
	// toto je take definovano na prototypu
	constructor(name, age) {
		this.name = name
		this.age = age
	}
	// tato metoda se definuje na prototypu
	sayName() {
		console.log(this.name);	
	}
}

let human = new Human("John", 34); // automaticky zavolá i constructor()

// pomocí extends se rozšíří i prototyp
class FPlayer extends Human {
	constructor(name, age, team) {
		super(name, age) // moderne zapsane Human.call(name, age)
		this.team = team
	}

	sayTeam() {
		console.log(this.team)	
	}
}
```
- mezi třídními metodami nejsou potřeba středníky
- vše uvnitř třídy je `use strict`
- gettery, settery:
```js
class User {
	constructor(name) {
		this._name = name; // toto zavola setter	
	}
	get name() {
		return this._name;
	}
	set name(value) {
		if(value.length < 4) {
			alert("Name is too short");
			return;
		}
		this._name = value;
	}
}

let user = new User("John");
alert(user.name); //John

user = new User(""); // Name is too short
```
- relativní novinka v JS jsou class props - ale jsou to individuální objekty, nejsou na prototypu
```js
class User {
	name = "John";
}
```