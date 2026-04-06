Zajímavé odkazy a materiály
- https://javascript.info/
- https://johnresig.com/apps/learn/ - pokročilý JS od tvůjce jQuery
- pokročilejší témata
	- https://exploringjs.com/es6/
	- https://ponyfoo.com/
- https://github.com/getify/You-Dont-Know-JS - kniha You Don't Know JS Yet
- https://www.patterns.dev/posts/classic-design-patterns/ - kniha Javascript Design Pattern
- https://nodejs.org/api/ - dokumentace [[Node.js, NPM, NVM|Node.js]]
- https://github.com/denysdovhan/wtfjs - What the fuck Javascript - různé zvláštnosti JS

- https://refactoring.guru/ - super stránka na dobré vysvětlení design patternů a refactoringu
***
### Obecné informace
Transpilace = můžu psát v moderním JS, ale pokud potřebuju "převést" kód na starší verze, to je proces transpilace
Typescript, Coffeescript, Dart... se kompilují do Javascriptu

- běhové prostředí (runtime) = nějaký JS engine a API
	- Firefox = JS engine = spidermonkey, API je DOM, canvas...
	- Node.js = engine = v8 + fs, http, zlib...

- JS je skriptovací jazyk, nemá žádné binárky
- JS je dynamický jazyk, dá se spustit i když obsahuje chyby
- JS je slabě typovaný (proto se nyní preferuje Typescript na trhu)

- není multithreadový, jen [[Javascript - Asynchronní programování|asynchronní]] 
	- asynchronní zpracování je jenom management zpracování (nějaké procesy běží na pozadí)
- na webu jen reaguje na události (eventy) a neběží na pozadí

##### Striktní režim
- [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode) - změny oproti defaultnímu "sloppy" módu
- lze napsat globálně
- lze napsat třeba jen pro konkrétní funkci
```js
"use strict";
```
# Vanilla JS
### [[Javascript - knihovny, zdroje]]

### [[Javascript - Syntaxe + stringy + čísla]]
### [[Javascript - Monkey patching a Polyfill metod]]
### [[Javascript - Objekty]]
### [[Javascript - Arrays]]
### [[Javascript - Funkce]] 

### [[Javascript - Context (this)]]
### [[Javascript - OOP, Prototypy]]
### [[Javascript - Asynchronní programování]]
### [[Javascript - DOM]]
### [[Javascript - Eventy]]
### [[Javascript - Iterátory a Generátory]]

### [[AJAX]]

