# PIVOT TABLE 

Taking the same theme from the last question, suppose you also wanted to
know the distribution of these appointment “categories” over the 7 days of the week. To
do so, create a cross-tabulation or pivot table where the appointment categories (angry,
ruminations, etc) are down the first column in alphabetical order and the days of the
week are across the first row as column headings. Counts of how many appointments of
each category are starting on each day should be the data represented within the pivot
table. You can assume that these start times are determined by the appointment local
time zone in each instance. 



SQL to generate pivot table:

```sql
SELECT *
FROM crosstab( 
    $$
    SELECT
     
        CASE 
            WHEN apt_topic ILIKE '%angry%' THEN 'angry'
            WHEN apt_topic ILIKE '%Ruminations%' THEN 'ruminations'
            WHEN apt_topic ILIKE '%Important_topic%' THEN 'important_topic'
            WHEN apt_topic ILIKE '%love%' THEN 'love'
            WHEN apt_topic ILIKE '%Thoughts%' THEN 'thoughts'
            END topic_type, 
        date_part('dow', start_time AT TIME ZONE apt_timezone) as dow, 
        count(*)
        FROM hw8.appointments 
        GROUP by topic_type, dow 
        ORDER BY topic_type, dow
    $$, 
    $$
        SELECT DISTINCT date_part('dow', start_time AT TIME ZONE apt_timezone) as dow FROM hw8.appointments
        ORDER BY dow

    $$

) AS
(
    topic_type TEXT,
    sun INT,
    mon INT,  
    tues INT,
    wed INT,
    thurs INT,
    fri INT,
    sat INT 
);


```
