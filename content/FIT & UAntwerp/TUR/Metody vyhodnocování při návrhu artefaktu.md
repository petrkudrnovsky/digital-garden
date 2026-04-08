Je to analýza výsledku dané fáze, ptáme se na otázky a snažíme se na ně upřímně odpovědět. Hodně se zaměřujeme na hledání příčin existujících problémů.

Záleží na aktuálním uživateli, v jaké fázi [[UCD (User Centered Design)|UCD]] se nacházíme, kde (v jakém prostředí) se nacházíme, jak je reprezentován artefakt (je to náčrt, prototyp, je dokončený) atd.
Osoba může být reálná osoba, tester, "hrající" HCI expert.

### [[Prediktivní metody v UCD]]
### [[Interpretativní metody v UCD]]
### [[Testování použitelnosti v UCD]]

Hlavní rozdělení:
- Různé metody hodnocení použitelnosti se liší tím, kdo se účastní, jaké úlohy plní a jaké prostředí je použito.

```mermaid
flowchart TD
    A["Reální uživatelé, Reálné úlohy, Reálný artefakt, Reálné prostředí, Reálné motivace"]
    B["Najatí uživatelé, Řízené úlohy, Reálný artefakt, Napodobené prostředí"]
    C["Najatí uživatelé, Řízené úlohy, Reálný artefakt, Napodobené prostředí"]
    D["Uživatelé hrají experty, Řízené úlohy, Prototyp"]

    A -->|"cíl: porozumění"| E[Interpretivní metody]
    B -->|"cíle: kontrola, porozumění"| F[Testování použitelnosti]
    C -->|"cíl: měření"| G[Kvantitativní lab. metody]
    D -->|"cíl: kontrola, predikce"| H[Expertní vyhodnocení / prediktivní metody]
```

Čtyři hlavní skupiny metod:
- Interpretivní metody - vycházejí z pozorování skutečných uživatelů v reálném kontextu, cílem je hluboké porozumění
- Testování použitelnosti - najatí uživatelé plní řízené úlohy s reálným artefaktem v simulovaném prostředí, cílem je kontrola a porozumění
- Kvantitativní laboratorní metody - podobné podmínky jako testování použitelnosti, ale zaměřené na měření výkonu
- Expertní a prediktivní metody - místo skutečných uživatelů hodnotí experti za použití prototypu, cílem je kontrola a předpovídání problémů

