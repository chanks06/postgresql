# PROMPT TOPIC 

Here, you want to determine how many appointments using each general starting prompt
exist in the dataset and output that information in a single table. Let’s also order said
table by descending number of appointments. 



Complete the below table with your counts:

| prompt          | count |
|:----------------|:-----:|
| angry           |  107  |
| ruminations     |  104  |
| important_topic |  100  |
| love            |   96  |
| thoughts        |   93  |


Supporting sql:

```sql
WITH topics (apt_topic, category)
AS (
SELECT apt_topic, 
CASE 
    WHEN apt_topic ILIKE '%angry%' THEN 'angry'
    WHEN apt_topic ILIKE '%Ruminations%' THEN 'ruminations'
    WHEN apt_topic ILIKE '%Important_topic%' THEN 'important_topic'
    WHEN apt_topic ILIKE '%love%' THEN 'love'
    WHEN apt_topic ILIKE '%Thoughts%' THEN 'thoughts'
    END category 
FROM hw8.appointments)

SELECT category as prompt, count(category) as count 
FROM topics
GROUP BY category
ORDER BY count DESC; 


```
