
## The following questions were answered by querying a dataset of metal bands. 


## Part A
What is the earliest formed band in the data set?
 - **Answer: Scorpions**
 - **Description: ordering rows by year formed in ascending order will make the top row of the result of this query the answer.**

```sql
SELECT *
FROM metal_bands 
ORDER BY formed; 
```

## Part B
How many rows are duplicated?
 - **Answer: 50**
 - **Description: The first query returned 4999 rows in the table. The Second query returns the number of distinct rows in the table, 4949. The difference between the two is the number of duplicates, 50.**

```sql
SELECT count(*) FROM metal_bands; 
SELECT DISTINCT * FROM metal_bands;
```

## Part C
How many bands have the colors black or white appearing somewhere in their name?
 - **Answer: 62**
 - **Description: I did query with WHERE 'white' or 'black' appeared in name using ILIKE. 63 hits returned. There is a duplicate Black Sabbath row, so 62 bands is the answer.**

```sql
SELECT band_name FROM metal_bands 
WHERE band_name ILIKE '%white%' OR band_name ILIKE '%black%'; 
```

## Part D
What is the second most popular German "Viking folk" style band?
 - **Answer: Falkenbach**
 - **Description: some bands have multiple styles, so I used ILIKE with % on both sides. Ordering fans by descending, the second row is Falkenbach.**

```sql
SELECT * 
FROM metal_bands
WHERE origin = 'Germany' AND style ILIKE '%Viking folk%'
ORDER BY fans DESC; 
```

## Part E
What is the oldest band in the database that has not yet split?
 - **Answer: Budgie**
 - **Description: filtered table where split is null (meaning band has not split yet), then ordered by formed. Because order by is by default ascending, the top row is our answer.**

```sql
SELECT * FROM metal_bands 
WHERE split IS NULL 
ORDER BY formed;
```
