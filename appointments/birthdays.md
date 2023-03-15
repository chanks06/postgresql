# Birthdays


How many users are attending meetings on their birthday?
- **Answer: 13 ** 
```sql
SELECT DISTINCT p.user_id, make_date(2022, b_month::int, b_day::int) as birthdays
FROM
(SELECT date_part('month', birthdate) as b_month,  
       date_part('day', birthdate) as b_day,
       user_id
                 FROM hw8.users) as sub 
JOIN hw8.participants as p ON
sub.user_id = p.user_id
JOIN hw8.appointments as a ON
p.appointment_id = a.appointment_id
WHERE p.is_attending LIKE 'Yes' AND  
a.start_time::date = make_date(2022, b_month::int, b_day::int);
```
