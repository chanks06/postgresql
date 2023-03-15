# New Year’s Resolutions dataset (resolutions.csv) contains just over 5k tweets that people made at the start of 2015 containing the hashtag #NewYearsResolution.

Create a table within your database called resolutions, set some reasonable data types
for the columns, and then import the data from the CSV file. 

## Precursors
 - SQL to generate needed table
```sql
CREATE TABLE resolutions (
    other_topic text, 
    res_topics text, 
    writer_gender text, 
    writer_name text, 
    res_category text, 
    retweet_count numeric, 
    tweet_text text, 
    tweet_coordinates text, 
    tweet_time_date timestamp, 
    tweet_date date,
    tweet_id double precision,
    city__state text,
    tweet_state text, 
    user_timezone text, 
    tweet_region text 
);

```
 - SQL to populate table
```sql
COPY resolutions 
FROM '/Users/charleshanks/Desktop/MSDS/DATA_503/hw-02-chanks06-main/data/resolutions.csv'
WITH (FORMAT CSV, HEADER);

```


## Part : How many different general categories are the resolutions sorted into?

 - **Answer: 10**
```sql
SELECT DISTINCT res_category FROM resolutions; 
```

## Part B : Who was the last person to post a primary topic about “time management” before the start of the new year?
- **Answer: MarkJMelton**
```sql
SELECT writer_name, tweet_time_date FROM resolutions
WHERE res_topics ILIKE '%time management%'
    AND tweet_date < '2015-01-01'
ORDER BY tweet_time_date DESC; 
```

## Part C : What female from the Midwest got the greatest number of retweets?
- **Answer: VeronicaRoth**
```sql
SELECT writer_name, retweet_count FROM resolutions
WHERE retweet_count > 0 
    AND writer_gender ILIKE '%female%'
    AND tweet_region ILIKE '%Midwest%'
ORDER BY retweet_count DESC; 
```

## Part D : Some individuals included more hashtags than just the #NewYearsResolution tag. Export to a CSV called hashtag happy.csv a table of all the individuals from
## Oregon that included more than one hashtag in their tweet.
```sql
COPY (
    SELECT writer_name, tweet_text FROM resolutions 
    WHERE tweet_state ILIKE '%OR%'
    AND tweet_text ILIKE '%#%#%'
    ORDER BY writer_name
)

TO '/Users/charleshanks/Desktop/MSDS/DATA_503/hw-02-chanks06-main/data/hashtag_happy.csv'
WITH (FORMAT CSV, HEADER);
```
