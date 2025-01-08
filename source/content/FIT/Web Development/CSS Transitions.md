Pokud chci v CSS udělat jen hezký přechod mezi 2 stavy nějakého elementu, tak se na to hodí *transition*, ne *animation*, animace je na tenhle úkon moc velké dělo.

Transition nastavím elementu, na kterém ta změna bude probíhat a nastavím ji parametry, které potřebuji (zpoždění, průběh atd.) + čas, jak dlouho poběží (mohu ho uložit do [[Proměnné v CSS|proměnné]] pro přehlednost).

Pak se tato *transition* provede vždy, když element změní specifikované vlastnosti (např. width, font-size atd.) -> toho mohu docílit např. přidáním dalších vlastností pomocí Eventu, pomocí pseudotříd (:hover, :focus).

Veškeré příklady a syntax naleznu na [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Transitions/Using_CSS_transitions) .
Na [CSS Tricks](https://css-tricks.com/almanac/properties/t/transition/) najdu také informace k *transition* + níže jsou blogové články, které používají *transition* v různých případech.
A klasicky nejlepší český popis na [Vzhůru dolů](https://www.vzhurudolu.cz/prirucka/css3-transitions).