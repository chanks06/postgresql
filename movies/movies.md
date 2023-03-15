# The movies dataset (movies.csv) contains over 9k movies with information pulled from The Movie Database website. 

## Precursors
 - SQL to generate needed table
```sql
CREATE TABLE movies (
    index int,
    md_id_num int, 
    title text, 
    release_date date, 
    overview text, 
    popularity numeric, 
    vote_average numeric, 
    vote_count numeric, 
    video boolean
);
```
 - SQL to populate table
```sql
COPY movies 
FROM '/Users/charleshanks/Desktop/MSDS/DATA_503/hw-02-chanks06-main/data/movies.csv'
WITH (FORMAT CSV, HEADER);
```


## Part A
 - **Answer: 1968**
```sql
SELECT * FROM movies 
WHERE overview ILIKE '%zombies%'
ORDER BY release_date;
```

## Part B
- **Answer: The Little Mermaid**
```sql
SELECT * FROM movies
WHERE release_date >= '1980-01-01' AND release_date <= '1989-12-31'
ORDER BY popularity DESC; 
```

## Part C 
- **Answer: Parasite, Spider-Man: Into the Spider-Verse, Whiplash, Interstellar, Inception**
```sql
SELECT * FROM movies 
WHERE vote_count >= 10000 AND release_date >= '2010-01-01'
ORDER BY vote_average DESC; 
```

## Part D

```sql
COPY (
    SELECT release_date, title, overview FROM movies
    WHERE (title NOT ILIKE '%Spider-Man%')
    AND (title ILIKE '%spider%' OR overview ILIKE '%spider%')
    ORDER BY release_date 
)

TO '/Users/charleshanks/Desktop/MSDS/DATA_503/hw-02-chanks06-main/data/spider_not_man.csv'
WITH (FORMAT CSV, HEADER);
```
