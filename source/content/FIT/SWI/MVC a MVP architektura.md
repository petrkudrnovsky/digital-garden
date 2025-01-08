MVC = model, view, controller
MVP = model, view, presenter
- velmi podobné principy, jsou založené na oddělení logiky od GUI
- využití u [[Návrh architektury aplikace#Třívrstvá aplikace|třívrstvých aplikací]] 

MVC používám často u [[Web Development#Symfony|Symfony]].  

![[Pasted image 20240106145152.png]]
- Model - data a logika
- View - zobrazování uživateli + zobrazovací logika
- Controller - prostředník - sdružuje View i Model

![[Pasted image 20240106145204.png]]
- View - hloupé (pasivní) zobrazování
- Model - definuje data, která se mají zobrazit
- Presenter - vybírá data z Modelu a formátuje je tak, aby se daly zobrazit
	- události od uživatele jdou za Presenterem, ten na základě nich mění Model a pak aktualizuje View
