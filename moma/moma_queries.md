# MOMA queries 


## Part A
What is the title of the piece of  art with the most collaborators/contributors at Museum of Modern Art? 
**Answer: Artifacts at the End of a Decade, with 47 collaborators**

```sql

--getting a count of collaborators per object_ids in artwork_artists

SELECT object_ids, count(constituent_ids) as num_of_collaborators FROM artwork_artists 
GROUP BY object_ids 
ORDER BY num_of_collaborators DESC; 

--47 collaborators. OK time to JOIN to find title of work: 

SELECT artworks.title, aa.object_ids, count(aa.constituent_ids) as num_of_collaborators FROM artwork_artists as aa 
JOIN artworks 
ON artworks.object_id = aa.object_ids
GROUP BY artworks, title, aa.object_ids 
ORDER BY num_of_collaborators DESC
LIMIT 1;

```



## Part B
Who are the top 5 painters (those whose work falls within the Painting classification) to have the most artwork acquired by the MoMA?
**Answer: Pablo Picasso, On Kawara, Henri Matisse, Jacob Lawrence, Batiste Madalena**

```sql
SELECT artists.display_name, constituent_ids, count(object_ids) as num_of_works FROM artwork_artists as aa
JOIN artists ON 
artists.constituent_id = aa.constituent_ids
JOIN artworks ON 
artworks.object_id = aa.object_ids
WHERE artworks.classification ILIKE '%Painting%'
GROUP BY artists.display_name, aa.constituent_ids
ORDER BY num_of_works DESC
LIMIT 5;

```



## Part C
What is the median age of a piece of art when the museum acquires it?
**Answer: 21**
```sql

CREATE TABLE artwork_age (
    year_created INT, 
    year_acquired INT,
    age INT 
);


INSERT INTO artwork_age (year_created, year_acquired)
SELECT substring(date FROM '\d{4}')::INT,
       extract(year from artworks.date_acquired)::INT 
FROM artworks; 

UPDATE artwork_age 
SET age = year_acquired - year_created;

SELECT * FROM artwork_age; 

SELECT percentile_cont(0.5) WITHIN GROUP (ORDER BY age) as median_age 
FROM artwork_age; 

--21 

```



## Part D
What artist has pieces in the MoMA collection across the greatest number of different classifications?
**Answer: George Maciunas, with pieces from 17 different classifications**

```sql
SELECT artists.display_name, count(DISTINCT artworks.classification) as classification_counter FROM artworks
JOIN artwork_artists  
ON artworks.object_id = artwork_artists.object_ids
JOIN artists
ON artists.constituent_id = artwork_artists.constituent_ids 
GROUP BY artists.display_name
ORDER BY classification_counter DESC;

```



