![[Use_Case_Model.pdf]]
- Případy užití už mohou být formou zadání pro programátory (už vidím aktéry, jaké přesně dělají činnosti apod.)
- Tvoří základ pro tvorbu uživatelské příručky a také slouží jako podklad k tvorbě akceptačních testů

Skládá se z:
1) Seznam účastníků/aktérů
2) Diagram případů užití
3) Seznam případů užití (popis a slovní rozšíření diagramu případů užití)
	- zde lze nadefinovat hlavní a alternativní scénáře + různé výjimky
	- také musím specifikovat podmínky provedení

![[Pasted image 20230520212811.png]]
==čas je také jeden z aktérů== - v aplikaci mohu mít automaticky spouštěné úlohy na základně nějaké časové události

### \<\<include\>\>
- když mám nějakou část použitou v několika případech užití, tak ji mohu vyjmout, udělat z ní samostatný případ užití a pak ji "naincludovat" do původních případů užití (využití DRY principu)
- povinné zahrnutí případu užití
![[Pasted image 20230520213559.png]]

### \<\<extends\>\>
- používá se, pokud je vyčleněná část scénáře nepovinná
![[Pasted image 20230520213617.png]]

### Doporučení při tvorbě
- popisuj, co má systém dělat, ne JAK to má dělat
- nerozepisuj se u "nezajímavých" UC
- diagram je pouze doplňující, hlavní úsilí jde do popisu a do scénářů
- jako doplnění scénáře užití může sloužit také návrh grafiky (wireframes, Figma, náčrty, nákresy...)
- v popisu scénáře se popisuje hlavně interakce uživatele se systémem (tedy jeden bod je akce uživatele a druhý bod je reakce systému atd.)
- Systém a Čas jsou aktéři pouze v případě pokud vykonávají něco **nezávisle** na uživatelské akci
	- kdybych tam zaznačoval jenom reakce systému, tak budu mít aktéra Systém, který bude ukazovat na všechny Use Cases, což nemá moc smysl, ani vypovídající hodnotu

- pokud je analytický tým oddělený od vývojového, tak je nutné minimalizovat nutnou komunikaci (právě fakt dobrým a podrobným diagramem užití)
- pokud je výsledkem projektu nějaký framework či knihovna, tak je třeba zvážit, jestli je potřeba takový diagram vůbec vytvářet

![[Pasted image 20230520214623.png]]
\+ každý případ užití musí mít svého aktéra