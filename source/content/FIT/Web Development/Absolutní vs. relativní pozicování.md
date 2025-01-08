Poznámka o `position` property v CSS. Defaultní hodnota je `static`. Někdy je potřeba posunout nějaké elementy mimo jejich základní flow. K tomu použiju právě `position`. 

### `position: relative;`
- funguje úplně stejně jako `static`, samotné přidání k elementu vůbec nic nezmění, ale umožní mi to začít měnit pozici pomocí atributů `top, bottom, right, left` a to RELATIVNĚ vůči svému rodiči a RELATIVNĚ vůči své originální pozici v document flow (tedy k té pozici, kde by normálně měl být)
### `position: absolute;` 
- pokud použiji `absolute` pozicování, tak daný element úplně vytrhnu z document flow, tedy jeho místo zaplní jiné elementy
- tedy jejich poloha není určená jejich původní polohou v document flow, ale vůči jejich předchůdci (neříkám vůči přímému rodiči, protože to tak nemusí být) - ==je to vůči prvnímu předchůdci, který má nastavený `position: relative;`==

- pokud chci dát absolutně pozicovaný element nad/pod/vedle jeho (relativně pozicovaného rodiče), tak můžu použít
```css
.parent {
	position: relative;
}

.child {
	position: absolute;
	bottom: 100%; // pro umístění nad rodičem
	top: 100%; // pro umístění pod rodičem
	
	left: 100%; // vedle rodiče...
	top: 0; // pro zarovnání

	bottom: calc(100% + 3px); pro 3px odsazení od rodiče
}
```