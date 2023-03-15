
# SUPERHEROES 

## Part A
What percentage of bad or evil superheros have red eyes?
- **Answers: 11.32 % of bad superheroes have red eyes **
```sql

SELECT count(*) FROM hw4.superhero AS s
JOIN hw4.color as c
ON s.eye_color_id = c.id
WHERE s.eye_color_id = 23 AND s.alignment_id = 2;


--24 bad superheros with red eyes 

SELECT count(*) FROM hw4.superhero AS s 
JOIN hw4.alignment as a
ON s.alignment_id = a.id
WHERE s.alignment_id = 2;

--212 bad superheros 

SELECT (24 / 212::numeric)*100;

```

## Part B
What is the average intelligence of human superheros?
- **Answer: 84.71 is the average intelligence of the human superhero.**

```sql
SELECT avg(a.attribute_value) FROM hw4.hero_attribute as a
JOIN hw4.superhero as s
ON a.hero_id = s.id
WHERE s.race_id = 24 AND a.attribute_id = 1; 

```

## Part C
Who is the heaviest superhero that can fly?
- **Answers: Fin Fang Foom**

```sql
SELECT sh.superhero_name, sh.weight_kg FROM hw4.superhero as sh
JOIN hw4.hero_power as hp 
ON sh.id = hp.hero_id
WHERE hp.power_id = 9 AND sh.weight_kg IS NOT NULL
ORDER BY sh.weight_kg DESC; 
```

## Part D
What is the most common superpower for superheros with blond hair?
- **Answer: STAMINA**
```sql


SELECT hp.power_id,
    count(hp.power_id) AS sp_counter
FROM hw4.superhero as sh
JOIN hw4.hero_power as hp
ON sh.id = hp.hero_id 
WHERE sh.hair_color_id = 6
GROUP BY hp.power_id
ORDER BY sp_counter DESC; 

SELECT sp.power_name FROM hw4.superpower as sp 
WHERE sp.id = 26; 
```

## Part E
How many unique combinations of race and gender have no corresponding superheros?
- **Answer: 105**
```sql
SELECT DISTINCT g.id AS g_id, r.id AS r_id FROM hw4.gender as g 
CROSS JOIN hw4.race as r
LEFT JOIN hw4.superhero as sh 
ON g.id = sh.gender_id AND r.id = sh.race_id
WHERE sh.superhero_name IS NULL;
```

