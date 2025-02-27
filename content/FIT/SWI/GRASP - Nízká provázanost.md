"Zodpovědnost přiřaď tak, aby byla vzájemná provázanost mezi prvky co nejmenší."

Provázanost (= coupling) je míra toho, jak moc je jeden prvek (= třída, subsystém modul) provázaný s dalšími prvky (= má o nich informace nebo na ně spoléhá).

Chci mít nízkou provázanost mezi objekty, protože pokud jsou hodně provázané, změny v jednom ovlivňují druhý a je obtížné je používat odděleně. Nejde je pochopit izolovaně a k tomu je zhoršená znovupoužitelnost.

Pozor na extrémy, určitá míra provázanosti mezi prvkami je normální a běžná. Nechci mít aplikaci tvořenou velkými a těžkopádnými objekty, které jsou mezi sebou minimálně provázané.

Zároveň nemusím moc řešit provázanost s prvky, které se moc nebo skoro vůbec nemění. Tam je to jedno, problém je u "nestabilních" prvků, které se často mění.

O tomto přístupu uvažuj v souvislosti s [[GRASP - Informační expert]] a [[GRASP - Vysoká soudržnost]]
