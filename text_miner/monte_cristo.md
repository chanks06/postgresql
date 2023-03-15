# Texting Mining _The Count of Monte Cristo_

## Part A
SQL to add and populate new table columns for the chapter number and chapter title:
```sql
ALTER TABLE count_of_mc 
ADD COLUMN chapter_number INT; 

ALTER TABLE count_of_mc 
ADD COLUMN chapter_title TEXT; 

--grabbing chapter numbers cast as int from chapter_heading 
SELECT (regexp_match(chapter_heading, '([0-9]{1,3})[.]'))[1]::INT FROM count_of_mc; 

--updating chapter_number with this regex extraction
UPDATE count_of_mc 
    SET chapter_number = (regexp_match(chapter_heading, '([0-9]{1,3})[.]'))[1]::INT;

--grabbing chapter title from chapter_heading 
SELECT (regexp_match(chapter_heading, '[.][ ](.*)'))[1] FROM count_of_mc; 

--updating chapter_title with this extraction: 
    SET chapter_title = (regexp_match(chapter_heading, '[.][ ](.*)'))[1];


```

## Part B
SQL to add and populate new table column to house tsvectorized contents, and add an index.
```sql
ALTER TABLE count_of_mc ADD COLUMN vector_contents tsvector; 

UPDATE count_of_mc 
    SET vector_contents = to_tsvector(chapter_contents);

CREATE INDEX mc_idx ON count_of_mc USING GIN (vector_contents); 




```

## Part C
What is the sum of all the chapter numbers which include a reference to "revenge"?
**Answer: 1509** 
```sql
SELECT sum(chapter_number) FROM count_of_mc
WHERE vector_contents @@ to_tsquery('revenge');

```
