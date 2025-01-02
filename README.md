# ETL Proces pre databázu MovieLens

## 1. Úvod a popis zdrojových dát

### 1.1 Téma projektu
Tento projekt sa zameriava na analýzu údajov z databázy MovieLens. Dáta zahŕňajú informácie o používateľoch, filmoch, žánroch a používateľských hodnoteniach. Hlavným cieľom projektu je vytvoriť ETL proces v Snowflake na prípravu dát pre analýzu a budovanie modelu, ktorý umožňuje:
- Určiť populárne filmy a žánre.
- Analyzovať preferencie používateľov.
- Skúmať správanie používateľov na základe hodnotení.

---

### 1.2 Typ dát
Databáza obsahuje štruktúrované dáta vrátane číselných, textových a časových polí. Formát dát je MySQL dump a CSV súbory, ktoré sú spracované a nahrané do Snowflake.

---

### 1.3 Popis jednotlivých tabuliek
#### `age_group`
- **Popis**: Kategórie vekových skupín používateľov.
- **Stĺpce**:
  - `id`: Unikátny identifikátor vekovej skupiny.
  - `name`: Názov vekovej skupiny (napr. "18-24").

#### `occupations`
- **Popis**: Zoznam povolaní používateľov.
- **Stĺpce**:
  - `id`: Unikátny identifikátor povolania.
  - `name`: Názov povolania (napr. "inžinier").

#### `users`
- **Popis**: Informácie o používateľoch.
- **Stĺpce**:
  - `id`: Unikátny identifikátor používateľa.
  - `age`: Vek používateľa.
  - `gender`: Pohlavie používateľa.
  - `zip_code`: PSČ používateľa.
  - `occupation_id`: Identifikátor povolania.
  - `age_group_id`: Identifikátor vekovej skupiny.

#### `ratings`
- **Popis**: Hodnotenia filmov používateľmi.
- **Stĺpce**:
  - `id`: Unikátny identifikátor hodnotenia.
  - `user_id`: Identifikátor používateľa.
  - `movie_id`: Identifikátor filmu.
  - `rating`: Hodnotenie (1–5).
  - `rated_at`: Dátum a čas hodnotenia.

#### `movies`
- **Popis**: Informácie o filmoch.
- **Stĺpce**:
  - `id`: Unikátny identifikátor filmu.
  - `title`: Názov filmu.
  - `release_year`: Rok vydania filmu.

#### `genres`
- **Popis**: Žánre filmov.
- **Stĺpce**:
  - `id`: Unikátny identifikátor žánru.
  - `name`: Názov žánru (napr. "komédia").

#### `genres_movies`
- **Popis**: Prepojenie medzi filmami a ich žánrami.
- **Stĺpce**:
  - `id`: Unikátny identifikátor záznamu.
  - `movie_id`: Identifikátor filmu.
  - `genre_id`: Identifikátor žánru.

---

### 1.4 ERD diagram
Nasledujúci diagram znázorňuje vzťahy medzi tabuľkami:
![ERD Diagram](https://github.com/user-attachments/assets/ac1a3229-be95-470e-b670-55aceeaaeffb)

---

## 2. Návrh dimenzionálneho modelu

### 2.1 Popis dimenzionálneho modelu
Pre projekt bol navrhnutý multi-dimenzionálny model typu hviezda. Tento model umožňuje efektívnu analýzu hodnotení filmov používateľmi. Model obsahuje faktovú tabuľku `fact_ratings` a niekoľko dimenzionálnych tabuliek.

---

### 2.2 Hlavné tabuľky
#### `fact_ratings` (Faktová tabuľka)
- Obsahuje kľúčové metriky a prepojenie na dimenzie.
- **Stĺpce**:
  - `rating_id`: Unikátny identifikátor hodnotenia.
  - `rating`: Hodnotenie (1–5).
  - `rated_at`: Dátum a čas hodnotenia.
  - `user_id`: Identifikátor používateľa.
  - `movie_id`: Identifikátor filmu.
  - `dim_time_time_id`: Identifikátor času.

#### Dimenzionálne tabuľky:
- **`dim_users`**: Obsahuje informácie o používateľoch.
- **`dim_movies`**: Obsahuje údaje o filmoch.
- **`dim_genres`**: Obsahuje zoznam žánrov filmov.
- **`dim_time`**: Obsahuje informácie o čase (dátum, mesiac, rok).

---

### 2.3 ERD diagram dimenzionálneho modelu
![Dimenzionálny model ERD](https://github.com/user-attachments/assets/cfcf6cb7-98c7-4b2e-87dc-cb67daa4f8d6)

---

## 3. ETL proces v Snowflake

### 3.1 Popis krokov
#### **1. Extract (Extrahovanie dát)**
- Dátové súbory vo formáte CSV boli nahrané do Snowflake pomocou STAGE.
- **Príkaz**:
  ```sql
  CREATE OR REPLACE STAGE movielens_stage;
### 3.2 Výsledky analýzy
- Top 10 filmov s najvyšším hodnotením:
  ```sql
  SELECT 
    m.title,
    mar.avg_rating,
    mar.total_ratings
  FROM movie_avg_ratings mar
  JOIN movies m ON mar.movie_id = m.id
  ORDER BY mar.avg_rating DESC
  LIMIT 10;
- Top 10 žánrov podľa počtu filmov:
  ```sql
  SELECT * FROM genre_movie_count
  ORDER BY total_movies DESC
  LIMIT 10;
### 3.3 Vizualizácie
* Navrhnite vizualizácie, ktoré ukazujú:
- Priemerné hodnotenia filmov.
- Počet filmov podľa žánrov.
- Preferencie používateľov podľa vekových skupín.
## 4. Vizualizácia dát

V tejto sekcii projektu sa zameriavame na prezentáciu vizuálnych analýz, ktoré poskytujú hlbšie pochopenie údajov o filmoch a používateľoch z databázy MovieLens. Vizualizácie sú navrhnuté tak, aby odpovedali na kľúčové otázky a odhalili zaujímavé trendy a vzorce v dátach.

### 4.1 Priemerné hodnotenia filmov
**Popis:** Graf zobrazuje priemerné hodnotenia pre top hodnotené filmy.  
**Účel:** Identifikuje filmy s najvyššími hodnoteniami, čo môže pomôcť pri odporúčaniach pre používateľov alebo pri analýze kvality filmových obsahov.

### 4.2 Počet filmov podľa žánrov
**Popis:** Tento graf prezentuje rozdelenie filmov podľa žánrov.  
**Účel:** Poskytuje prehľad o popularite jednotlivých žánrov, čo môže byť užitočné pre filmové distribučné spoločnosti alebo marketérov.

### 4.3 Rozloženie hodnotení filmov
**Popis:** Zobrazuje distribúciu hodnotení medzi používateľmi.  
**Účel:** Analyzuje ako používatelia hodnotia filmy a môže odhaliť prípadnú prísťnosť alebo štedrosť v ich hodnoteniach.

### 4.4 Počet hodnotení podľa vekových skupín
**Popis:** Graf ukazuje počet hodnotení podľa vekových skupín používateľov.  
**Účel:** Skúma, ako vekové rozdiely ovplyvňujú filmové preferencie a hodnotenie.

### 4.5 Historický vývoj počtu filmov
**Popis:** Tento graf ilustruje trend v počte vydaných filmov počas rôznych rokov.  
**Účel:** Umožňuje sledovať ako sa kinematografia vyvíjala v čase a reaguje na zmeny v kultúrnych a technologických trendoch.

### Náhľady vizualizácií
Pre lepšiu predstavivosť sú tu príklady vizualizácií vygenerovaných z našich analytických procesov:

![Priemerné hodnotenia filmov](https://github.com/sebastiansKychatyi/MovieLens-ETL-Process/blob/main/images/average_ratings.png)
![Počet filmov podľa žánrov](https://github.com/sebastiansKychatyi/MovieLens-ETL-Process/blob/main/images/movie_counts_by_genre.png)
![Rozloženie hodnotení filmov](https://github.com/sebastiansKychatyi/MovieLens-ETL-Process/blob/main/images/rating_distribution.png)
![Počet hodnotení podľa vekových skupín](https://github.com/sebastiansKychatyi/MovieLens-ETL-Process/blob/main/images/ratings_by_age_group.png)
![Historický vývoj počtu filmov](https://github.com/sebastiansKychatyi/MovieLens-ETL-Process/blob/main/images/historical_movie_trends.png)

  
