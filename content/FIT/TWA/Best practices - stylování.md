1) používat [Normalise.css](https://necolas.github.io/normalize.css/) 
	- alternativa k CSS resetům
	- "normalizuje" všechny styly napříč všemi prohlížeči
	- s tím začít projekt a normalizační soubor dát nahoru

2) preferovat mobile-first přístup při tvorbě webovek
	- výhody jsou rychlejší vývoj
	- místo (max-width) dávám (min-width) a tyhle styly jsou vidět celou dobu v DevTools
	- nutí mě to se zaměřit na to nejdůležitější na webovce
	- další proč: https://www.lukew.com/ff/entry.asp?933

3) nastav si v html tagu `font-size: 10px;` (klidně i jinou hodnotu) a pak všechny velikost uváděj v rem jednotkách (10px je pro to dobrý, že 1.6rem je pak 16px)
	- výhoda je, že mohu rychle měnit tenhle jediný font-size a proporčně se mi zvětšuje stránka a nic se nerozbije (pokud to nepřeháním do extrému)
	- font-size ale pak nemůžu nastavovat jinde (jinak než pomocí rem)

4) ideální je vůbec nepoužívat px. Stačí mi jenom relativní jednotky. Absolutní slouží k tisku např. abych to mohl napozicovat správně. Můžu mít také speciální CSSka pro projektor (kde mám taky fixní šířku a výšku).

5) nepoužívat přímo černou, ale třeba `#111111`, vypadá jako černá

6) funkce `minmax(min, max)` v CSS a obecně [Funkce v CSS](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Functions) 

7) `width: max-content` šířka bude vždy maximum contentu - často se hodí, další viz [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/width) 

8) použít [Alternative CSS box model](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/The_box_model#the_alternative_css_box_model) ala `{box-sizing: border-box}` - který drží šířku elementu v rámci ohraničení - takže šířka ohraničení a paddingu se počítá v celkové šířce a nepříčítá se jako u [Stardardního CSS box modelu](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/The_box_model#the_standard_css_box_model)