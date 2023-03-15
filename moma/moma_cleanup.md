# Problem 1
- List of any other students you worked with: N/A
- About how long did you work on this problem?: 1 hour


## Part A

Create the initial artists and artworks tables in whatever database you
like, and copy the information in from the included CSV files into the appropriate
tables. If you are using the template code I provided for you, this should be simple
and you need to only include the code you used to copy in the table information.
If you made your own tables with your own chosen names, please include the code
for them as well.

Here is SQL to create two tables that will nicely import the provided CSV files:
```sql
CREATE TABLE artworks (
  title TEXT,
  artist TEXT,
  constituent_id TEXT,
  artist_bio TEXT,
  nationality TEXT,
  begin_date TEXT,
  end_date TEXT,
  gender TEXT,
  date TEXT,
  medium TEXT,
  dimensions TEXT,
  credit_line TEXT,
  accession_number TEXT,
  classification TEXT,
  department TEXT,
  date_acquired DATE,
  cataloged TEXT,
  object_id INTEGER,
  url TEXT,
  thumbnail_url TEXT,
  circumference_cm DOUBLE PRECISION,
  depth_cm DOUBLE PRECISION,
  diameter_cm DOUBLE PRECISION,
  height_cm DOUBLE PRECISION,
  length_cm DOUBLE PRECISION,
  weight_kg DOUBLE PRECISION,
  width_cm DOUBLE PRECISION,
  seat_height_cm DOUBLE PRECISION,
  duration_sec DOUBLE PRECISION
);

CREATE TABLE artists (
  constituent_id BIGINT,
  display_name TEXT,
  artist_bio TEXT,
  nationality TEXT,
  gender TEXT,
  begin_date INTEGER,
  end_date INTEGER,
  wiki_qid TEXT,
  ulan TEXT
);

```

Provide your SQL to import the data
```sql
COPY artworks 
FROM '/Users/charleshanks/Desktop/MSDS/DATA_503/hw-07-chanks06-main/data_csvs/Artworks.csv'
WITH (FORMAT CSV, HEADER);

COPY artists 
FROM '/Users/charleshanks/Desktop/MSDS/DATA_503/hw-07-chanks06-main/data_csvs/Artists.csv'
WITH (FORMAT CSV, HEADER);
```


## Part B

Create the artwork_artists table such that it has one row for each artwork id and
contributing artists.

SQL to create and populate the desired `artwork_artists` table:
```sql

CREATE TABLE artwork_artists (
  object_ids INT, 
  constituent_ids TEXT
);

INSERT INTO artwork_artists (object_ids, constituent_ids)
SELECT object_id, unnest(
  string_to_array(artworks.constituent_id, ', ')
) FROM artworks; 

SELECT * FROM artwork_artists;
```


## Part 

You might think your artwork_artists table looks great! You are probably wrong, unfortunately. In theory, each piece of art with a corresponding artist
should show up once in your created table. Is that the case? Can you figure out
why this happened? Fix it by removing the duplicates (or recreating the table
with only unique combinations). While you are there, the constituent_ids are
just increasing integers, so it would be nicer to work with them as actual integers
instead of the strings they came in as when you split the original string. (And importantly, this removes any extra spaces that might be still lurking around as well)
Change that column data type to be an integer

SQL to cleanup duplicates and column types:

```sql
--Checking for duplicates 
SELECT object_ids, constituent_ids, count(*) 
FROM artwork_artists
GROUP BY object_ids, constituent_ids
HAVING count(*) > 1;

SELECT DISTINCT object_ids, constituent_ids
FROM artwork_artists; 

--removing table with duplicates
DROP TABLE artwork_artists; 

--recreating table
CREATE TABLE artwork_artists (
  object_ids INT, 
  constituent_ids TEXT
);

--adding DISTINCT to query in inserting values 
INSERT INTO artwork_artists (object_ids, constituent_ids)
SELECT DISTINCT object_id, unnest(
  string_to_array(artworks.constituent_id, ', ')
) FROM artworks; 

--Checking for duplicates after changing table insertion to distinct
SELECT object_ids, constituent_ids, count(*) 
FROM artwork_artists
GROUP BY object_ids, constituent_ids
HAVING count(*) > 1;

--it worked, no duplicates found.

--changing artwork.artists.constituent_ids from TEXT datatype to INT 
ALTER TABLE artwork_artists ALTER COLUMN constituent_ids SET DATA TYPE INT USING constituent_ids::INT;  

--checking to make sure data in constituent_ids column are now integers 
SELECT pg_typeof(constituent_ids) FROM artwork_artists;

```


## Part D

You now have three related tables, but no constraints are in place. Alter the tables to add primary keys object_id and constituent_id to artworks
and artists, respectively. For artwork_artists, create a compound primary key
that includes both columns, which we can do since we just confirmed that those
combinations are all unique. Add in the necessary foreign keys as well to link the
artwork_artists table columns back to the necessary reference table columns.

SQL to setup constraints and keys
```sql
--Creating primary keys for artworks and artists tables 
ALTER TABLE artworks ADD CONSTRAINT artworks_key PRIMARY key (object_id); 
ALTER TABLE artists ADD CONSTRAINT artists_key PRIMARY KEY (constituent_id); 

--creating compound primary key for both columns of artwork_artists table 
ALTER TABLE artwork_artists ADD CONSTRAINT compound_aa_key PRIMARY KEY (object_ids, constituent_ids); 

--adding foreign keys to artwork_artists to reference original tables
 ALTER TABLE artwork_artists ADD CONSTRAINT constituent_ids_foreign_key FOREIGN KEY (constituent_ids) REFERENCES artists (constituent_id); 

ALTER TABLE artwork_artists ADD CONSTRAINT object_ids_foreign_key FOREIGN KEY (object_ids) REFERENCES artworks (object_id);
```


## Part E

Now that we have better related the tables, there is really no need for the
artworks table to still have all the columns containing information that is already
in the artists table. Remove those 7 columns from artworks.

SQL to remove columns of duplicated information from `artworks`:
```sql

--Drop 7 redundant columns in artowrks table: 
ALTER TABLE artworks DROP COLUMN artist, DROP COLUMN constituent_id, DROP COLUMN artist_bio, DROP COLUMN nationality, DROP COLUMN begin_date, DROP COLUMN end_date, DROP COLUMN gender;

--checking to make sure 7 columns were removed: 
SELECT * FROM artworks; 
```


## Part F

If you check for missing information in the artists table, you’ll realize that there is plenty, but much of it is information that we probably do not
have. One thing you might notice though is that there are artists with no recorded
nationality, but who do have something about a nationality mentioned in their
bio. These probably could be fixed, but there are a lot of them and no good automatic way to do so, so I’m not going to ask that of you! One other aspect of
this table though is that missing or “not yet occurring” birth and death dates are
recorded with 0’s, which seems potentially problematic if you wanted to do calculations with those columns. Change all instances of 0 in the birth year (begin_date)
or death year (end_date) to NULL instead.

SQL to `NULL` out missing birth date or death date values:
```sql

UPDATE artists
SET begin_date = NULL 
WHERE begin_date = 0;

UPDATE artists
SET end_date = NULL 
WHERE end_date = 0;

```


## Part G

Moving over to the artworks table, take a look at the date column, which is when the artwork was supposed created. This column is an absolute mess
of formatting. Some are years, some are ranges of years, some are specific days, and everything in between. For the most part though, each record contains at least
one 4 digit date somewhere amidst the text. While grabbing just this 4 digit date may not be perfectly precise, it would surely be more useful than the current state
of things.

SQL to create and populate new `date_int` column:
```sql
SELECT date FROM artworks; 
SELECT * FROM artworks; 

ALTER TABLE artworks ADD COLUMN date_int INT; 

UPDATE artworks 
SET date_int = substring(date FROM '\d{4}')::INT;

--confirming that extracting year worked: 
SELECT sum(date_int) FROM artworks; 

--263617360

```
