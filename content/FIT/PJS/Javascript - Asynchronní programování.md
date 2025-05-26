Javascript nepodporuje multithreading (je jednovláknový)
- v NodeJS je možné mít implementaci více vláken, ale je to spíše workaround a v nativním Javascriptu multithreading není
### Callstack
- datová struktura (zásobník), který reprezentuje, kde přesně v programu se nacházíme
	- jednotlivé volané funkce se v momentě zavolání vloží na callstack a jakmile dají return, tak se zase popnou
	- při nekontrolované rekurzi se na stack vkládá další a další volání a jednou mi přeteče
### Event loop
- asynchronní řešení u Javascriptu
- velmi jednoduchá funkce
	- dívá se na task queue (kam [[#Web Apis]] vkládají hotové asynchronní funkce - tzv. callback funkce, které mají běžet po tom, co se dokoná nějaká asynchronní aktivita) a na [[#Callstack]]
	- jakmile je callstack prázdný, tak tam hodí první funkci z task queue
		- právě dokončená asynchronní funkce se nemůže jen tak hodit do běžící jiné funkce (musí počkat, až skončí)

Synchronní řešení:
```js
// regres.in
function get(url) {
  // dostupné pouze v browseru
  const xhr = new XMLHttpRequest();
  xhr.open('GET', url, false); // pouze konfiguruje spojení, false znamená synchronní
  xhr.send(); // odešli požadavek
  console.log(JSON.parse(xhr.responseText));
}

get("https://reqres.in/api/users?page=2");
```
- zde se console.log() vykoná, než se vykoná request pomocí `send()`, to je aktivní čekání ://
- když dám `true`, tedy je to asynchronní, `console.log()` hodí prázdný string, protože se to vykoná dříve než se to stihne stáhnout
- blokující funkce (resp. pomalé funkce) jsou blbé, protože prohlížeč není schopen dělat nic jiného než tyhle funkce doběhnou (ani nejde mačkat tlačítka atd.)
```js
// nastavím event - co se stane, když se změní state té asynchronní funkce
xhr.onreadystatechange = function() {
	// readyState = 4 je stav, kdy už je to hotové
	if(xhr.readyState === 4) {
		if(xhr.status === 200) {
			console.log(JSON.parse(xhr.responseText));
		} else {
			console.log(new Error(`Chyba: ${xhr.status}!`).message);
		}
	}
}
```
- toto je asynchronní, ale naprd, je to hodně omezené, nemám možnost návratové hodnoty atd.
	- mohu do parametrů dát funkce (callbacky) na návratové hodnoty => tím se velmi lehce dostanu do [callback hellu](http://callbackhell.com/) (nebo se tomu říká "pyramid of doom")
### Callback-style
- asynchronní funkci (nebo resp. funkci, která dělá nějakou asynchonní akci - tedy wrapperu) dáme do argumentu callback, který se zavolá, až ta funkce skončí (např. reaguje na event, že async akce skončila)
	- příklady tady: https://javascript.info/callbacks
		- je tam i ukázaný poměrně častý recipe "error-first-callback"
- tenhle přístup je fajn pro malé akce, či pokud nepotřebujeme v callbacku používat další callback a další callback a další atd. => to vede ke [callback hellu](http://callbackhell.com/) (zvlášť, když mám více asynchronních akcí závisejících na sobě)
### Web Apis
- sem se registrují asynchronní funkce
- jakmile jsou ready na puštění, tak se hodí do Callback Queue a tam je nekonečný loop (Event loop), který kontroluje, jestli v něm něco není a pokud je, tak až bude volný Call Stack, tak se to tam vloží a vykoná
- normální funkce (synchronně volané) se dávají ihned na Call Stack (ihned se vykonají)
- pěkná [Webová reprezentace Loupe od Phillipse Robertse](http://latentflip.com/loupe/?code=JC5vbignYnV0dG9uJywgJ2NsaWNrJywgZnVuY3Rpb24gb25DbGljaygpIHsKICAgIHNldFRpbWVvdXQoZnVuY3Rpb24gdGltZXIoKSB7CiAgICAgICAgY29uc29sZS5sb2coJ1lvdSBjbGlja2VkIHRoZSBidXR0b24hJyk7ICAgIAogICAgfSwgMjAwMCk7Cn0pOwoKY29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0KCkgewogICAgY29uc29sZS5sb2coIkNsaWNrIHRoZSBidXR0b24hIik7Cn0sIDUwMDApOwoKY29uc29sZS5sb2coIldlbGNvbWUgdG8gbG91cGUuIik7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D) 
	- a video o [Event loopu](https://www.youtube.com/watch?v=8aGhZQkoFbQ&ab_channel=JSConf) 
### Promise
- super materiál zde: https://javascript.info/promise-basics
- vlastními slovy bych to popsal jako slib, že jakmile tato funkce doběhne, tak dá vědět funkcím (které si o to řekly pomocí `.then()`, `.catch()` a `.finally()`) buď, že doběhla v pořádku a tím pádem předá návratovou hodnotu a nebo že doběhla s chybou a tím pádem předá Error
- je to objekt reprezentující asynchronní operaci, která může skončit úspěšně (resolve) nebo neúspěšně (reject)
```js
// takhle vypadá volání resolve()
let promise = new Promise(function(resolve, reject) { 
	setTimeout(() => resolve("Done!"), 1000);
});

// takhle vypadá volání reject()
let promise = new Promise(function(resolve, reject) { 
	setTimeout(() => reject(new Error("Whoops!")), 1000);
});

promise.then(
	result => alert(result), // shows "done!" after 1 second 
	error => alert(error) // doesn't run 
);
```
- pomocí then() specifikuji funkce (neboli callbacky), které proběhnou, pokud Promise skončí úspěšně či neúspěšně
- má tři stavy: pending, fulfilled a rejected

- je to klasický objekt, který v sobě má data, status a APIčko
```js
function sleep(ms) {
	return new Promise((resolve) => {
		setTimeout(() => {
			resolve();
		}, ms)
	});
}
// .then() je součást API Promise
sleep(2000).then(() => {
	console.log("Utekly 2 vteriny"); // tato funkce se zavolá on resolve()
});

const p = sleep(2000);
/*
	{
		// value
		// status = 'PENDING', 'FULLFILLED', 'REJECTED'
		// onFullfilled
		// onRejected
	}
*/

// takhle lze registrovat více funkcí na resolve
p.then(() => {
	console.log('ahoj');
});

p.then(() => {
	console.log('nazdar');
});
```
- status mohu měnit pouze jednou (jakmile zavolám `reject`, dostanu REJECTED a už to nemohu měnit)
- value se používá pro posílání výsledku/návratové funkce z asynchronní funkce (lze mít pouze jeden argument, více argumentů se ignoruje)
- onFullfilled je array funkcí, které se zavolají, pokud se zavolá `resolve()`, onRejected je to stejné, akorát s `reject`
	- tyto funkce stanovuji jako arrow funkce při vytváření Promise objektu
	- pro jeden objekt (Promise) mohu registrovat více funkcí jako resolve, reject
- pokud se registruje nová funkce na resolve a daný Promise už proběhl, tak to nevadí, spustí se to ihned (za předpokladu, že Promise proběhl dobře -> resolve)
- všechny vyhozené Errory **v rámci Promise** handluje `.catch()`, ale pokud jsou vyhozené mimo Promise, tak to skončí chybou
- pokud vracím už třeba hned resolvnutý Promise, tak i když je hned resolvnutý, tak stejně to čeká na synchronní kód, a pak až to naskočí (viz Event loop v JS)
	- je to dobrý způsob, jak spustit "synchronní" akci až po tom, co se vyprázdní callstack

#### .then(f,f) 
- lze volat i `.then(f)` - to je jenom pro resolve
- volání `.then()` vrátí nový promise, na kterým pak lze volat další `.then()` => tomu se říká Promise chaining (více zde: https://javascript.info/promise-chaining)
- pozor: přiřazení více `.then()` jednomu Promisu zvlášť není chaining

#### .catch(f)
- je to jenom zkratka pro `.then(null, f)` <- tedy pro určení reject funkce
- pak pokud se chainuje, tak může například na konci řetězce chytat všechny errory, tedy pokud někde před tím nějaký `.then()` hodí `.reject()` nebo přímo vyhodí `new Error()`, tak to propadne až k prvnímu catchi
	- zde: https://javascript.info/promise-error-handling

#### .finally()
- toto je určené pro finální "úklid" či dodělání něčeho po doběhu Promise, ať už dopadne jakkoliv, funkce nepřijímá argumenty, protože nemá sloužit k resolvnutí či ošetřování výstupu z Promise

#### Statické metody na Promise
- `Promise.all([promise1, promise2])`
	- = počkej na všechny Promises, vrací Promise
	- jakmile min. jeden z Promisů rejectne, tak ten Promise, který to vrací bude také REJECTED
	- pokud array obsahuje non-promise elementy, tak se ignorují, ale stále jsou v návratové hodnotě (vrací `Promise { <state>: "fulfilled", <value>: Array[2] }`
		- podle toho, jak se resolvne

### Async, await
- moderní přístup (syntaktický sugar pro práci s Promises), díky nim mohu psát asynchronní kód stejným způsobem jako synchronní
```js
async function x() {
	return 1; // tato jednička je zabalená do Promise objektu (ve skutečnosti)
}
// takhle se pak k jedničce dostanu
x().then(jednicka => console.log(jednicka));
```
- `async` funkce vždy vrací Promise (pokud vracím jenom hodnotu, tak to stejně zabalí do Promise)
```js
async function x() {
	await sleep(2000);
	console.log(Math.random());
	await sleep(2000);
	console.log(Math.random());
	await sleep(2000);
	console.log(Math.random());
}
```
- není blokující, velmi jednoduchý zápis 
- `await` lze použít jen v `async` funkcích, jednoduše zastaví vykonávání funkce, dokud se nedokončí daný Promise
	- ale není to aktivní čekání, JS v době čekání může provádět jinou práci
	- pokud se do `await` vrátí Error (neboli `.reject()`), tak se to chová jako `throw Error()`
- pokud chci chytit vyhozený Error, tak musím použít `try {...} catch {...}` konstrukci

```js
// tyto dvě funkce se chovají stejně, u té první nemusí být 'async'
async function f1() {
	return Promise.resolve(5); // vrátí už rovnou resolvnutý Promise
	return Promise.reject(6); // vrátí už rovnou rejectnutý Promise
}
async function f1() {
	return 5;
}
```

Pokud na sobě joby nezávisí, tak nevolat pod sebou `await, await, await`, ale spíš použít `await Promise.all()` a nasázet je tam (pojedou simultálně a ne za sebou)

```js
promises = [];
Promise.all(promises)
	.then(/* arr of resolved promises */) // všechny resolvnuté nebo prázdné
	.catch(/* first reject */) // když se rejectne první (a tím pádem výsledný Promise je rejected)

Promise.allSettled(promises)
	// v results je array values
	.then(results => results.forEach((result) => console.log(result.status)))
	.catch()

Promise.any(promises)
	.then(/* first resolved */) // jakkykoliv, který bude rychlejší
	.catch(/* arr of rejects */) // pokud jsou všechny rejected 

Promise.race(promises)
	.then()
	.catch()
```