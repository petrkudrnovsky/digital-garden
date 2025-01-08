"Zodpovědnost přiděl tomu prvku, který má informace potřebné ke splnění této zodpovědnosti."

Nemusím tak získávat data získávat z jiných míst - nejsme závislí na dalších prvcích a podporujeme [[GRASP - Nízká provázanost|nízkou provázanost]].

Pokud žádný nemá všechny informace, volím toho, kdo jich má nejvíce, nebo má nejhůře získatelné a další si obstará sám (z jiných míst, vypočítá, sestaví...).

Kde se to nehodí? Např. při zodpovědnosti uložení dat do databáze. Z tohoto principu by každý objekt měl implementovat rozhraní pro uložení sama sebe do databáze a to kvůli problémům s [[GRASP - Vysoká soudržnost|vysokou soudržností]] a nízkou provázaností.