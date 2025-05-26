Zdroj: https://www.vzhurudolu.cz/prirucka/css-aspect-ratio
***
- Jde o způsob, jak vytvářet kontejnery pro obsah (hlavně obrázky), aby držely místo, než se obsah načte.
- Pokud prohlížeči trvá načíst obsah, obrázek, iframe atd., tak je místo něho bílý prostor nebo nic a při načtení onoho obsahu stránka "poskočí", což nechceme.

Mám nějaký container a obrázek v něm s určenými atributy width a heigh, ty drží poměr stran samotného obrázku.
```html
<p class="box">
  <img src="https://satyr.dev/4000x3000/green?delay=3000&text=IMG with WIDTH/HEIGHT" width="2000" height="1500" alt="Image">
</p>  
```
A pro placeholder, dokud se nenačte obrázek se použije container .box, kterému nastavím aspect-ratio stejné, jako je poměr stran obrázku.
```css
/* Placeholder color: */

.box {
  aspect-ratio: 4/3;
  background: grey;
}

/* Responsive/Fluid image: */

.box img {
  max-width: 100%;
  height: auto;
  display: block;
}
```

### Aspect-ratio v kombinaci object-fit a object-position
![[Pasted image 20221124152245.png]]

- pokud mám na stránce různý obsah s různými poměry stran, tak díky aspect-ratio a object-fit mohu držet jednotný poměr stran


### Defaultní nastavení
- v prohlížečích je defaultní nastavení aspect-ratio pro různé obsahy takto:
```css
img, input[type="image"], video, embed, iframe, marquee, object, table {
  aspect-ratio: attr(width) / attr(height);
}
```
- atribut attr() - umožní do CSS vložit hodnotu příslušného atributu z HTML
	- zde tedy hodnoty width a height z HTML