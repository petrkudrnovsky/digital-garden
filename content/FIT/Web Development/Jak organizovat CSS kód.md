Zdroj: https://www.vzhurudolu.cz/prirucka/css-metodiky

- organizovat svoje CSS styly chci, protože se v nich chci později vyznat, zejména pokud jich je větší množství
- existuje více způsobů a metodik, jak CSS styly organizovat
- Martin Michálek ze Vzhůru dolů doporučuje kombo SUIT CSS a ITCSS
- u těchlech stylů chybí sémantika
- styly jsou u těchto přístupů více spoléhá na HTML a tolik se necachují
- tyto přístupy zachovávají nizší [[CSS#Specificita CSS Selektory Selektorů|specificitu]]
- nevyužívají potenciál HTML5 atributů
## [[OOCSS - objektové CSS]]
- nejstarší známá metodika, je základem ostatních metodik, ale přímo raw verzi této skoro nikdo nepoužívá
- OOCSS jede na zapouzdření stylů do samostatných objektů (nebo komponent)
## [[CSS BEM - pojmenovávací konvence]]
- obalení a rozšíření OOCSS
- velmi rozšířené, také používá komponenty, každá komponenta se nazývá *blok*, dceřinný prvek je *element* a obměna komponenty jako *modifikátor*
## SUIT CSS
- dokumentace: https://suitcss.github.io/
- preprocesor: https://github.com/suitcss/preprocessor
- dále staví na BEM a k tomu přidává focus na další důležité prvky - *stavy* a *utility*
- oproti BEM mění pojmenovávání
- stavy - vedle úprav vzhledu (modifikátory v BEMu) přidává změnu chování (tu obvykle ovládá JavaScript - např. animace)
- utility - v podstatě jednoúčelové třídy, kdy název selectoru odpovídá tomu, co ty třída dělá (takový atomičtější Bootstrap)
## ITCSS (Inverted Triangle CSS)
- zdroj na doplnění: https://frontend.garden/clanky/proc-je-itcss-nejpokrocilejsi-metodika-organizace-css/
- metodika pro dělení a organizaci souborů do jednotlivých typů
- ![[Pasted image 20221121150606.png]]
	- prvky, které jsou nahoře mají nízkou specificitu selektorů, ale ovlivní větší množství prvků na stránce
- základní typy prvků podle ITCSS
- ![[Pasted image 20221121150716.png]]