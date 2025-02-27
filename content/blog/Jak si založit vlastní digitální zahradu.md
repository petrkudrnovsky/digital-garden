---
draft: false
---
Líbí se vám moje zahrada? A chtěli byste si taky založit a publikovat svoji? Pokud ano, tak máte moji plnou podporu!

V tomto krátkém příspěvku vás nasměruji na zdroje, díky kterým sestavil a publikoval zahradu, na kterou se právě díváte.
# Markdown + Quartz + Github
Je to vcelku jednoduché. Jediné, co potřebujete je mít vlastní obsah, který na web chcete dát (bez toho to moc nedává smysl ;)), k tomu [Quartz](https://quartz.jzhao.xyz/), což je generátor statických stránek napsaný v Typescriptu. V podstatě je to software, který vaše poznámky v Markdown formátu převede na HTML stránku tak, aby se dala pěkně zobrazit v prohlížeči.

Poslední část, Github se postará o publikování vaší digitální zahrady. Přímo v dokumentaci [Quartz](https://quartz.jzhao.xyz/) je i návod, jak zprovoznit propojení s Githubem a s Github Actions, aby se synchronizované poznámky ihned publikovaly na web.

Pak své poznámky najdete zdarma publikované na `<username>.github.io`. Kdykoliv, kdy synchronizujete poznámky z vašeho zařízení na Github, tak se Github automaticky postará o jejich publikování.

Pokud vlastníte svoji doménu, tak není vůbec problém používat tu. Stačí, pokud u svého poskytovatele u domény nastavíte A záznamy na Github servery (viz [Quartz - custom domain](https://quartz.jzhao.xyz/hosting#custom-domain))
- z mé zkušenosti a co jsem se pak dočetl na [dokumentaci Github](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site), tak by se měl přidat ještě záznam CNAME na subdoménu `www`, který by pak měl ukazovat na `<username>.github.io` - díky tomu bude pak Github moci vygenerovat HTTPS certifikát a vaše digitální zahrádka bude zabezpečená

> [!tip] Užitečné odkazy
> Užitečné odkazy:
> - oficiální dokumentace Quartz: https://quartz.jzhao.xyz/
> - [video](https://www.youtube.com/watch?v=6s6DT1yN4dw&ab_channel=NicolevanderHoeven) od Nicole van der Hoeven, jak nastavuje svoji digitální zahradu
> 	  - jinak, i další obsah od Nicole velmi doporučuji, je to jeden z předních tvůrců obsahu o digitálním mozku, chytrých poznámkách a digitálních zahradách
> - pro inspiraci přikládám digitální zahrádku přímo od tvůrce Quartzu: https://jzhao.xyz/
# Existuje jednodušší varianta?
Ano! Předchozí postup vyžaduje jisté technické dovednosti a také nějaké předispozice ve formě nainstalovaného [Node.js](https://nodejs.org/en), verzovacího nástroje [Git](https://git-scm.com/) a účtu na službě [Github.com](https://github.com/). 

Pokud pro správu a editování svých Markdown poznámek používáte Obsidian, tak ten přímo nabízí službu [Publish](https://obsidian.md/publish), kde můžete velmi jednoduše vybrat, které poznámky chcete publikovat na web a které ne a Obsidian všechno udělá za vás.

Kde je háček? Je to placená služba. Obsidian jako takový je celý zdarma, ale toto je funkce navíc, za kterou je potřeba zaplatit. Není to žádná velká částka, v době psaní tohoto příspěvku je to $8 měsíčně.

Co vám to přinese navíc narozdíl o řešení, které používám já?
- je to plně podporovaná funkce přímo od tvůrců Obsidianu
- jednoduchá možnost přidání vlastní domény a vlastních stylů (pomocí CSS a JavaScriptu)
- možnost "schovat" svoje poznámky za heslo, což je super funkce, pokud nechceme poznámky sdílet celému světu, ale jen pár lidem, kteří znají heslo
***
Závěrem, to byl krátký návod, spíš rozcestník na to, jak si založit vlastní digitální zahradu. Pokud byste k tomu měli jakékoliv otázky, určitě se mi ozvěte. A pokud si opravdu zahradu založíte, dejte mi vědět, hrozně rád se na ni podívám :) 