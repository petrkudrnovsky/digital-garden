### Array
Převést array-like object na array pomocí `Array.from(arrayLike)` nebo `[...arrayLike]` (spread operátor)
- array-like object je třeba string, jiný array, mapa
- pokud chci array klíčů z objektu: `Object.keys(obj)` nebo hodnoty: `Object.values(obj)`

**Array.prototype.slice(index od, index do)**
- bez argumentů vrátí kopii pole
- index do v záporných hodnotách se počítá zezadu (-1 = poslední znak)
- vrátí pole extractnutých elementů

**Array.prototype.splice()**
- lze pomocí toho mazat určité části pole + vkládat lib. počet prvků kamkoliv do pole
- `arr.splice(1, 3, "a", "b")` - od 1. indexu smaž 3 prvky a na jejich místo dej "a", "b"
- `splice(1, 0, 'Feb');` - insertne nový prvek na index 1
- `splice(4, 1, 'May')` - replacne prvek na indexu 4

**Array.prototype.sort()**
- sort() - sortí lexikograficky, lepší je uvést svůj callback
```js
arr.sort(function(a,b) {
	return b-a
})

// nastavi language-sensitive porovnavani stringu
const collator = new Intl.Collator("cs");
arr.sort(function(a,b) {
	return collator.compare(a,b);
})
```

```js
// 2 možnosti spojení 2 polí
console.log(arr.concat(arr2))
console.log([...arr, ...arr2])
```

#### Důležité metody
- forEach()
```js
arr.forEach(function(value, index, arr) {
 console.log(`${value} ${index} ${arr.length}`)
})
// lze také
arr.forEach(function(value) {
 console.log(`${value}`)
})
```
- filter()
	- funguje podobně jako forEach(), signature callbacku je stejný
	- narozdíl od forEach nevrací undefined, ale nové pole vyfiltrovaných hodnot
	- když vracím true, znamená to, že prvek chci, pokud false, tak prvek nechci (...mít ve vyfiltrovaných hodnotách)
- every() 
	- vrátí true, pokud všechny prvky v poli splňují nějakou podmínku
- some()
	- vrátí true, pokud aspoň nějaké prvky v poli splňují nějakou podmínku
- reduce()
	- "redukuje" pole  na jednu hodnotu, bere si initial hodnotu a mezivýpočet ukládá v akumulátoru a na konci do sum uloží finální hodnotu akumulátoru
	- pokud nechci nic v aktuálním loopu přidávat, tak dám jenom `return acc;`
```js
const sum = arr.reduce(function(acc, value, index, arr) {
	return acc + value;
}, 0) // <- initial hodnota
```
