# ETL Process for the MovieLens Database

## 1. Introduction and Data Overview

### 1.1 Project Theme
This project focuses on analyzing data from the MovieLens database. The data includes information about users, movies, genres, and user ratings. The main objectives are to develop an ETL process in Snowflake to prepare the data for analysis and create models that enable:
- Identification of popular movies and genres.
- Analysis of user preferences.
- Examination of user behavior based on ratings.

### 1.2 Data Type
The database contains structured data, including numerical, textual, and temporal fields. The data is provided in a MySQL dump format and needs to be processed and loaded into Snowflake.

### 1.3 Description of Tables
- **`age_group`**: Categories of user age groups.
  - **Columns**:
    - `id`: Unique identifier for the age group.
    - `name`: Name of the age group (e.g., "18-24").
- **`occupations`**: List of user occupations.
  - **Columns**:
    - `id`: Unique identifier for the occupation.
    - `name`: Occupation name (e.g., "Engineer").

*(Extend this section for other tables as needed.)*

### 1.4 ERD Diagram
The following diagram illustrates the relationships between the tables:

![MovieLens ERD](https://github.com/user-attachments/assets/ac1a3229-be95-470e-b670-55aceeaaeffb)

---

## 2. Dimensional Model Design

### 2.1 Dimensional Model Overview
A star schema has been designed for efficient analysis of user ratings. The model includes:
- **Fact table**: `fact_ratings`
- **Dimension tables**: `dim_users`, `dim_movies`, `dim_time`, and others.

### 2.2 Key Tables
- **`fact_ratings` (Fact Table)**:
  - **Attributes**:
    - `rating_id`: Unique rating identifier.
    - `rating`: Rating (1–5).
    - `rated_at`: Date and time of the rating.
    - `user_id`: User identifier (links to `dim_users`).
    - `movie_id`: Movie identifier (links to `dim_movies`).
    - `dim_time_time_id`: Time identifier (links to `dim_time`).

- **`dim_users` (User Dimension)**:
  - **Attributes**:
    - `user_id`: Unique user identifier.
    - `gender`: Gender.
    - `age_group_id`: Age group identifier.
    - `occupation_id`: Occupation identifier.
    - `zip_code`: User's ZIP code.

*(Extend this section for other dimension tables as needed.)*

### 2.3 Dimensional Model ERD
Visualization of the dimensional model:

![Dimensional Model ERD](https://github.com/user-attachments/assets/cfcf6cb7-98c7-4b2e-87dc-cb67daa4f8d6)

---

## 3. ETL Process in Snowflake

### 3.1 Main Steps in the ETL Process
The ETL process involves three main steps: **Extract**, **Transform**, and **Load**, implemented using Snowflake based on CSV source files.

### 3.2 Steps and SQL Commands

#### 3.2.1 Extract (Data Extraction)
Source data in CSV format was uploaded to a Snowflake stage.

**Command to create stage:**
```sql
CREATE OR REPLACE STAGE movielens_stage;
