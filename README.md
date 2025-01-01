ETL Proces pre databázu MovieLens
Úvod a popis zdrojových dát
1. Téma projektu
Tento projekt sa zameriava na analýzu údajov z databázy MovieLens. Dáta zahŕňajú informácie o používateľoch, filmoch, žánroch a používateľských hodnoteniach. Hlavným cieľom projektu je vytvoriť ETL proces v Snowflake na prípravu dát pre analýzu a budovanie modelu, ktorý umožňuje:

Určiť populárne filmy a žánre.
Analyzovať preferencie používateľov.
Skúmať správanie používateľov na základe hodnotení.
2. Typ dát
Databáza obsahuje štruktúrované dáta vrátane číselných, textových a časových polí. Formát dát je MySQL dump, ktorý je potrebné spracovať a nahrať do Snowflake.

3. Popis jednotlivých tabuliek
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
rating: Hodnotenie (1-5).
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
Popis: Žánre filmov

Hlavné stĺpce:
id: Unikátny identifikátor žánru.
name: Názov žánru (napr. "komédia").
4. ERD diagram
Nasledujúci diagram znázorňuje vzťahy medzi tabuľkami:
.![MovieLens_ERD](https://github.com/user-attachments/assets/f72dd756-0d32-460f-9cce-0944ec9721b6)
