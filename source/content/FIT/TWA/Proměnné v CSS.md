- deklaruji je pomocí '--', například 
- nejlépe je mít v jednom CSS souboru: `root.css` nebo `variables.css` a ten pak importovat do ostatních

```css
/* zde může být proměnná použita jenom ve scopu, ve kterém je definovaná */
element {
	--main-bg-color: brown;
}

/* --- */

/* definováno globálně */
:root {
	--main-bg-color: yellow;
}
```

- jsou case-sensitive
	- `--my-var` je něco jiného než `--My-var` 

- použití:

```css
h1 {
	background-color: var(--main-bg-color);
}

/* lze také mít fallback hodnotu, kdyby proměnná nebyla k dispozici */
p {
	color: var(--main-text-color, #FFFFFF);
}
```

Více informací:
MDN: https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties
Proměnné v Sassu: [[How To Sass]]