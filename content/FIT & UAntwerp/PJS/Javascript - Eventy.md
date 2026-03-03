```html
<div onclick="alert(1)"></div>
```
- toto není úplně šťastný způsob, nemůžu přidat více funkcí, které budou na tomto eventu
- je lepší to přidávat ve skriptu
	- `div.addEventListener('click', funkce)`
	- `div.removeEventListener('click', funkce)` - ta funkce nemůže být anonymní, abych ji mohl smazat (a tedy porovnávat), tak musí mít stejnou referenci (viz porovnávání objektů (=funkcí)) v JS

### .dispatchEvent()
- lze "emitovat" nějaký Event
	- `div.dispatchEvent(new Event('click'));` 

.addEventListener má ještě 3. parametr -> bool true/false
- podle toho, jestli chci daný Event spouštět v Capturing fázi (která jde o parenta k childu a spouští se jako první) nebo v Bubbling fázi (od childa k parentovi)
- viz: https://javascript.info/bubbling-and-capturing
![[Pasted image 20230504175418.png]]
- `e.stopPropagation()` - zastaví propagaci eventu v daném směru

```js
div.addEventListener('click', (event) => {
	console.log(event.target); // nejvic inner child, na kterem byl event spusteny
	console.log(event.currentTarget);
})
```

`e.preventDefault()`