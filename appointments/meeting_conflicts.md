# Conflicting Meetings


## Part A
What meetings did Simon plan to attend that conflict? Be sure to include all the desired information


**Answer:**
| appointment_id      | start_time | end_time | appointment_id2 | start_time | end_time |
| ----------- | ----------- | ----------- | ----------- | ----------- | ----------- | 
| 269      | 2022-03-11 15:15:00 | 2022-03-11 19:15:00 | 90 | 2022-03-11 16:00:00 | 2022-03-11 16:15:00 |
| 314   | 2022-03-17 17:15:00 | 2022-03-17 18:15:00 | 221 | 2022-03-17 17:15:00 | 2022-03-17 17:45:00 |



```sql
CREATE TEMP TABLE meetings AS

SELECT 
    a1.appointment_id, 
    a1.start_time, 
    (a1.start_time + a1.duration) AS end_time, 
    a1.duration

FROM hw8.appointments AS a1
JOIN hw8.participants AS p
    ON p.appointment_id = a1.appointment_id
WHERE p.user_id = 12 AND 
p.is_attending = 'Yes';


SELECT  a1.appointment_id, a1.start_time, a1.end_time,
        a2.appointment_id as appointment_id2, a2.start_time as start_time2, a2.end_time as end_time2
FROM meetings as a1
JOIN meetings as a2
ON a1.appointment_id <> a2.appointment_id
WHERE (a1.start_time AT TIME ZONE 'EST', a1.start_time AT TIME ZONE 'EST' + a1.duration ) OVERLAPS 
(a2.start_time AT TIME ZONE 'EST', a2.start_time AT TIME ZONE 'EST' + a2.duration )
AND 
a1.appointment_id > a2.appointment_id; 
```




