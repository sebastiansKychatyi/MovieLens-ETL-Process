<h1>ETL Proces pre databázu MovieLens</h1>

<h2>1. Úvod a popis zdrojových dát</h2>

<h3>1.1 Téma projektu</h3>
<p>
    Tento projekt sa zameriava na analýzu údajov z databázy MovieLens. Dáta zahŕňajú informácie o používateľoch, filmoch, žánroch a používateľských hodnoteniach. 
    Hlavným cieľom projektu je vytvoriť ETL proces v Snowflake na prípravu dát pre analýzu a budovanie modelu, ktorý umožňuje:
</p>
<ul>
    <li>Určiť populárne filmy a žánre.</li>
    <li>Analyzovať preferencie používateľov.</li>
    <li>Skúmať správanie používateľov na základe hodnotení.</li>
</ul>

<h3>1.2 Typ dát</h3>
<p>
    Databáza obsahuje štruktúrované dáta vrátane číselných, textových a časových polí. 
    Formát dát je MySQL dump, ktorý je potrebné spracovať a nahrať do Snowflake.
</p>

<h3>1.3 Popis jednotlivých tabuliek</h3>
<ul>
    <li>
        <strong>age_group</strong>: 
        <ul>
            <li>Popis: Kategórie vekových skupín používateľov.</li>
            <li>Hlavné stĺpce:
                <ul>
                    <li>id: Unikátny identifikátor vekovej skupiny.</li>
                    <li>name: Názov vekovej skupiny (napr. "18-24").</li>
                </ul>
            </li>
        </ul>
    </li>
    <li>
        <strong>occupations</strong>: 
        <ul>
            <li>Popis: Zoznam povolaní používateľov.</li>
            <li>Hlavné stĺpce:
                <ul>
                    <li>id: Unikátny identifikátor povolania.</li>
                    <li>name: Názov povolania (napr. "inžinier").</li>
                </ul>
            </li>
        </ul>
    </li>
    <!-- Добавьте остальные таблицы по аналогии -->
</ul>

<h3>1.4 ERD diagram</h3>
<p>Nasledujúci diagram znázorňuje vzťahy medzi tabuľkami:</p>
<img src="https://github.com/user-attachments/assets/ac1a3229-be95-470e-b670-55aceeaaeffb" alt="MovieLens_ERD">

<h2>2. Návrh dimenzionálneho modelu</h2>

<h3>2.1 Návrh dimenzionálneho modelu</h3>
<p>
    Pre projekt sme navrhli multi-dimenzionálny model typu hviezda, ktorý umožňuje efektívnu analýzu hodnotení filmov používateľmi. 
    Model obsahuje faktovú tabuľku <strong>fact_ratings</strong> a viacero dimenzií.
</p>

<h3>2.2 Hlavné tabuľky v modeli:</h3>
<ul>
    <li>
        <strong>fact_ratings (Faktová tabuľka):</strong>
        <ul>
            <li>Obsahuje kľúčové metriky a prepojenie na dimenzie.</li>
            <li>Atribúty:
                <ul>
                    <li>rating_id: Unikátny identifikátor hodnotenia.</li>
                    <li>rating: Hodnotenie (1–5).</li>
                    <li>rated_at: Dátum a čas hodnotenia.</li>
                    <li>user_id: Identifikátor používateľa (väzba na dim_users).</li>
                    <li>movie_id: Identifikátor filmu (väzba na dim_movies).</li>
                    <li>dim_time_time_id: Identifikátor času (väzba na dim_time).</li>
                </ul>
            </li>
        </ul>
    </li>
    <li>
        <strong>dim_users (Dimenzia používateľov):</strong>
        <ul>
            <li>Obsahuje údaje o používateľoch, ako vekové skupiny a zamestnanie.</li>
            <li>Atribúty:
                <ul>
                    <li>user_id: Unikátny identifikátor používateľa.</li>
                    <li>gender: Pohlavie.</li>
                    <li>age_group_id: Identifikátor vekovej skupiny.</li>
                    <li>occupation_id: Identifikátor povolania.</li>
                    <li>zip_code: PSČ používateľa.</li>
                </ul>
            </li>
        </ul>
    </li>
    <!-- Добавьте остальные таблицы по аналогии -->
</ul>

<h3>2.3 ERD diagram dimenzionálneho modelu</h3>
<p>Model je vizualizovaný na nasledujúcom diagrame:</p>
<img src="https://github.com/user-attachments/assets/cfcf6cb7-98c7-4b2e-87dc-cb67daa4f8d6" alt="Dimenzionálny model ERD">
<h2>3. ETL proces v nástroji Snowflake</h2> <h3>3.1 Hlavné kroky ETL procesu</h3> <p> ETL proces pozostáva z troch hlavných krokov: <strong>Extract</strong> (extrakcia dát), <strong>Transform</strong> (transformácia dát) a <strong>Load</strong> (nahratie dát). Tento proces bol implementovaný v Snowflake na základe zdrojových dát vo formáte CSV. </p> <h3>3.2 Kroky a SQL príkazy</h3> <h4>3.2.1 Extract (Extrahovanie dát)</h4> <p> Dáta boli pripravené vo formáte CSV a uložené v lokálnom súborovom systéme. Následne boli nahraté do Snowflake pomocou STAGE `movielens_stage`. </p> <p><strong>Príkaz na vytvorenie STAGE:</strong></p> <pre> CREATE OR REPLACE STAGE movielens_stage; </pre> <p> Soubory boli nahrané do STAGE cez užívateľské rozhranie Snowflake. </p> <h4>3.2.2 Load (Nahratie dát do tabuliek)</h4> <p> CSV súbory boli nahraté do príslušných tabuliek pomocou príkazu <code>COPY INTO</code>. </p> <p><strong>Príklady:</strong></p> <ul> <li><strong>Tabuľka `age_group`:</strong></li> <pre> COPY INTO age_group FROM @movielens_stage/age_group.csv FILE_FORMAT = (TYPE = 'CSV' FIELD_OPTIONALLY_ENCLOSED_BY = '"' SKIP_HEADER = 1); </pre> <li><strong>Tabuľka `users`:</strong></li> <pre> COPY INTO users FROM @movielens_stage/users.csv FILE_FORMAT = (TYPE = 'CSV' FIELD_OPTIONALLY_ENCLOSED_BY = '"' SKIP_HEADER = 1); </pre> <li>Podobné príkazy boli použité pre ďalšie tabuľky: <code>tags</code>, <code>ratings</code>, <code>movies</code>, <code>genres</code>, <code>genres_movies</code>.</li> </ul> <h4>3.2.3 Transform (Transformácia dát)</h4> <p> Na základe nahraných dát boli vytvorené odvodené tabuľky pre analýzu. </p> <p><strong>Príklady:</strong></p> <ul> <li><strong>Priemerne hodnotenie filmov:</strong></li> <pre> CREATE OR REPLACE TABLE movie_avg_ratings AS SELECT movie_id, AVG(rating) AS avg_rating, COUNT(rating) AS total_ratings FROM ratings GROUP BY movie_id; </pre> <li><strong>Priradenie používateľov k vekovým skupinám:</strong></li> <pre> CREATE OR REPLACE TABLE user_age_groups AS SELECT u.id AS user_id, u.age, ag.name AS age_group_name FROM users u JOIN age_group ag ON u.age_group_id = ag.id; </pre> <li><strong>Počet filmov podľa žánrov:</strong></li> <pre> CREATE OR REPLACE TABLE genre_movie_count AS SELECT g.name AS genre_name, COUNT(gm.movie_id) AS total_movies FROM genres g JOIN genres_movies gm ON g.id = gm.genre_id GROUP BY g.name; </pre> </ul> <h3>3.3 Výsledky transformácie</h3> <p> Po úspešnom dokončení ETL procesu boli vytvorené nové analytické tabuľky, ktoré umožňujú odpovedať na nasledujúce otázky: </p> <ul> <li><strong>Ktoré filmy majú najvyššie priemerné hodnotenie?</strong></li> <li><strong>Ktoré žánre majú najviac filmov?</strong></li> <li><strong>Aké vekové skupiny sú najaktívnejšie v hodnotení filmov?</strong></li> </ul> <h4>Príklady výsledných dotazov:</h4> <ul> <li><strong>Top 10 filmov podľa priemerného hodnotenia:</strong></li> <pre> SELECT m.title, mar.avg_rating, mar.total_ratings FROM movie_avg_ratings mar JOIN movies m ON mar.movie_id = m.id ORDER BY mar.avg_rating DESC LIMIT 10; </pre> <li><strong>Top 10 žánrov podľa počtu filmov:</strong></li> <pre> SELECT * FROM genre_movie_count ORDER BY total_movies DESC LIMIT 10; </pre> </ul> <h3>3.4 Záver</h3> <p> Tento ETL proces efektívne pripravil dáta z databázy MovieLens pre analytické účely. Umožňuje odpovedať na dôležité otázky o preferenciách používateľov, populárnych filmoch a správaní rôznych vekových skupín. </p>
