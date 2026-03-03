Iterátor je objekt, který v sobě má v sobě funkci `next()`, která vrací další object
- má done: false -> může se pokračovat
- done: true -> konec

Generátor generuje iterátor
```js
function* gen() {
	yield 1
	yield 2
	yield 3
}

for(const n of gen()) {
	console.log(n);
}
```
### Generátor
Generátor se na `yield` zastaví a pokračuje jakmile je někde zavolán znovu
- zapisuje se speciálně pomocí hvězdičky
- na `yield` se zastaví, vrátí hodnotu a při opětovné zavolání "pokračuje"
- generátor je iterovatelný objekt
	- a jde přes něj iterovat pomocí `for...of` cyklu
	- vrací totiž objekt typu `Iterator`
- využití genetárorů v praxi:
	- lazy evaluation (vrátí hodnotu až v momentě zavolání)
	- zpracování velkých dat (není potřeba je celé nahrávat do paměti)
	- v asynchronním zpracování
		- existují i `async function*() {}`, které kombinují výhody async/await a generátorů 
			- výhodné pro streamování dat (`yield await fetchData(1)`)
```js
function* makeZahlenIterator() {
  let zahl = 0
  while(zahl <= 50) {
    yield zahl
    zahl += 10
  }
}

const zahlenIterator = makeZahlenIterator()

for(const z of zahlenIterator) {
  console.log(z)
}
```