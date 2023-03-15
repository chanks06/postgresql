# POKEMON!

## Precursors
 - SQL to generate needed table
```sql
CREATE TABLE pokemon (
    id_num int,
    name text, 
    type_1 text,
    type_2 text, 
    stat_total int, 
    hp int,
    attack int,
    defense int,
    sp_atk int, 
    sp_def int, 
    speed int, 
    generation int, 
    legendary boolean
);
```
 - SQL to populate table
```sql
COPY pokemon 
FROM '/Users/charleshanks/Desktop/MSDS/DATA_503/hw-02-chanks06-main/data/pokemon.csv'
WITH (FORMAT CSV, HEADER);
```


## Part A: How many legendary pokemon might we consider “defensive”, in that they have a defense stat which is higher than their offense?
 - **Answer: 184**
```sql
SELECT count(*) FROM pokemon 
WHERE defense > attack AND sp_def > sp_atk; 
```

## Part B: How many pokemon have both water and poison as their two types?
- **Answer: 4**
```sql
SELECT count(*) FROM pokemon
WHERE (type_1 ILIKE 'Water' OR type_1 ILIKE 'Poison')
 AND (type_2 ILIKE 'Poison' OR type_2 ILIKE 'Water'); 
```

## Part C: What 2nd generation pokemon that is not a Mega form has the greatest sum of its stats? In case of a tie, choose the one whose name comes latest in the alphabet. 
- **Answer: Lugia**
```sql
SELECT * FROM pokemon
WHERE generation = 2
AND name NOT ILIKE '%Mega%'
ORDER BY stat_total DESC;
```

## Part D: Save to a CSV file (called type combos.csv) a table of all the unique combinations dual-type pokemon types. 

```sql
COPY (
    SELECT DISTINCT type_1, type_2
    FROM pokemon
    ORDER BY type_1
)
TO '/Users/charleshanks/Desktop/MSDS/DATA_503/hw-02-chanks06-main/data/type_combos.csv'
WITH (FORMAT CSV);
```
