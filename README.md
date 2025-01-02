
Вот красиво оформленный файл README.md для вашего проекта:

ETL Proces pre databázu MovieLens
1. Úvod a popis zdrojových dát
1.1 Téma projektu
Tento projekt sa zameriava na analýzu údajov z databázy MovieLens. Dáta zahŕňajú informácie o používateľoch, filmoch, žánroch a používateľských hodnoteniach.
Hlavným cieľom projektu je vytvoriť ETL proces v Snowflake na prípravu dát pre analýzu a budovanie modelu, ktorý umožňuje:

Určiť populárne filmy a žánre.
Analyzovať preferencie používateľov.
Skúmať správanie používateľov na základe hodnotení.
1.2 Typ dát
Databáza obsahuje štruktúrované dáta vrátane číselných, textových a časových polí.
Formát dát je MySQL dump, ktorý je potrebné spracovať a nahrať do Snowflake.

1.3 Popis jednotlivých tabuliek
age_group
Popis: Kategórie vekových skupín používateľov.
Hlavné stĺpce:
id: Unikátny identifikátor vekovej skupiny.
name: Názov vekovej skupiny (napr. "18-24").
occupations
Popis: Zoznam povolaní používateľov.
Hlavné stĺpce:
id: Unikátny identifikátor povolania.
name: Názov povolania (napr. "inžinier").
users
Popis: Informácie o používateľoch.
Hlavné stĺpce:
id: Unikátny identifikátor používateľa.
age: Vek používateľa.
gender: Pohlavie používateľa.
zip_code: PSČ používateľa.
occupation_id: Identifikátor povolania (odkaz na occupations).
age_group_id: Identifikátor vekovej skupiny (odkaz na age_group).
tags
Popis: Tagy pridané používateľmi k filmom.
Hlavné stĺpce:
id: Unikátny identifikátor tagu.
user_id: Identifikátor používateľa.
movie_id: Identifikátor filmu.
tags: Text tagu.
created_at: Dátum a čas vytvorenia tagu.
ratings
Popis: Hodnotenia filmov používateľmi.
Hlavné stĺpce:
id: Unikátny identifikátor hodnotenia.
user_id: Identifikátor používateľa.
movie_id: Identifikátor filmu.
rating: Hodnotenie (1–5).
rated_at: Dátum a čas hodnotenia.
movies
Popis: Informácie o filmoch.
Hlavné stĺpce:
id: Unikátny identifikátor filmu.
title: Názov filmu.
release_year: Rok vydania filmu.
genres_movies
Popis: Prepojenie medzi filmami a ich žánrami.
Hlavné stĺpce:
id: Unikátny identifikátor záznamu.
movie_id: Identifikátor filmu.
genre_id: Identifikátor žánru (odkaz na genres).
genres
Popis: Žánre filmov.
Hlavné stĺpce:
id: Unikátny identifikátor žánru.
name: Názov žánru (napr. "komédia").
1.4 ERD diagram
Nasledujúci diagram znázorňuje vzťahy medzi tabuľkami:
![MovieLens_ERD](https://github.com/user-attachments/assets/ac1a3229-be95-470e-b670-55aceeaaeffb)



2. Návrh dimenzionálneho modelu
2.1 Návrh dimenzionálneho modelu
Pre projekt sme navrhli multi-dimenzionálny model typu hviezda, ktorý umožňuje efektívnu analýzu hodnotení filmov používateľmi. Model obsahuje faktovú tabuľku fact_ratings a viacero dimenzií.

2.2 Hlavné tabuľky v modeli:
fact_ratings (Faktová tabuľka):
Obsahuje kľúčové metriky a prepojenie na dimenzie.
Atribúty:
rating_id: Unikátny identifikátor hodnotenia.
rating: Hodnotenie (1–5).
rated_at: Dátum a čas hodnotenia.
user_id: Identifikátor používateľa (väzba na dim_users).
movie_id: Identifikátor filmu (väzba na dim_movies).
dim_time_time_id: Identifikátor času (väzba na dim_time).
dim_users (Dimenzia používateľov):
Obsahuje údaje o používateľoch, ako vekové skupiny a zamestnanie.
Atribúty:
user_id: Unikátny identifikátor používateľa.
gender: Pohlavie.
age_group_id: Identifikátor vekovej skupiny.
occupation_id: Identifikátor povolania.
zip_code: PSČ používateľa.
dim_movies (Dimenzia filmov):
Obsahuje údaje o filmoch.
Atribúty:
movie_id: Unikátny identifikátor filmu.
title: Názov filmu.
release_year: Rok vydania filmu.
dim_genres a movies_genres (Dimenzia žánrov a vzťahy):
Obsahuje údaje o žánroch a vzťah medzi filmami a žánrami.
Atribúty:
genre_id: Identifikátor žánru.
name: Názov žánru.
dim_time (Dimenzia času):
Umožňuje analyzovať údaje podľa dátumu, mesiaca, roku a hodiny.
Atribúty:
time_id: Unikátny identifikátor času.
day, month, year, hour: Dátumové a časové údaje.
day_of_week: Deň v týždni.
2.3 ERD diagram dimenzionálneho modelu
Model je vizualizovaný na nasledujúcom diagrame:
![dim_movielens](https://github.com/user-attachments/assets/cfcf6cb7-98c7-4b2e-87dc-cb67daa4f8d6)


