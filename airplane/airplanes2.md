# Problem 2
- List any other students you worked with: N/A
- About how long did you work on this problem? 1 hour 


## Part A

Create a simple bar chart showing the total number of departing flights each day
of the week.


```sql
COPY (
SELECT w.day_name, count(depart_time) FROM hw6.flights as f 
JOIN hw6.weekdays as w ON
w.code = f.day_of_week
GROUP BY f.day_of_week, w.day_name)

TO '/Users/charleshanks/Desktop/MSDS/DATA_503/departing_flights_per_day'
WITH (FORMAT CSV, HEADER);
```

```r
library(tidyverse)
planes = read_csv('/Users/charleshanks/Desktop/MSDS/DATA_503/departing_flights_per_day')

order = c('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday')

ggplot(planes, aes(x = factor(day_name, level = order), y = count)) + 
  geom_bar(stat = "identity", fill = "light blue") + 
  labs(title = "Total number of departing flights per day of week",
       subtitle = "January 2020", 
       x = "Day of Week", 
       y = "Departing Flights")
```



## Part B

This second part is more open-ended. Define a question that you are interested in
investigating in this current data set. There is lots of potential data here that I
haven’t already asked questions about that might be interesting! The only requirement is that you must use GROUP BY in the course of answering your question, and
you must include a visualization to help you answer the question. Clearly state
both your question, your work to answer the question, and your visualization in
your submission.

**Question you are trying to answer: Which airline operating at PDX had the most cancelled flights in Jan 2020? Which had the least? **  
**Your findings: Southwest Airlines had the most cancellations, with 1921 flights cancelled. Hawaiian Airlines had the least, with 17 flights cancelled. **  
Don't forget to also upload your resulting visualization back to GitHub!

```sql
COPY (
SELECT c.name, sum(f.cancelled) as num_cancelled_flights FROM HW6.flights as f 
JOIN hw6.carriers as c 
ON c.carrier_id = f.carrier_id 
GROUP BY c.name 
HAVING sum(f.cancelled) != 0)

TO '/Users/charleshanks/Desktop/MSDS/DATA_503/pdx_cancelled_flights'
WITH (FORMAT CSV, HEADER);
```

```r
pdx_cancelled_flights = read_csv('/Users/charleshanks/Desktop/MSDS/DATA_503/pdx_cancelled_flights')

ggplot(pdx_cancelled_flights, aes(y = reorder(name, num_cancelled_flights), x = num_cancelled_flights)) + 
  geom_point() + 
  geom_point(data = pdx_cancelled_flights[which.max(pdx_cancelled_flights$num_cancelled_flights),], color = "red", size = 3) + 
  geom_text(data = pdx_cancelled_flights[which.max(pdx_cancelled_flights$num_cancelled_flights),], aes(label = num_cancelled_flights), nudge_x = -100)+
  geom_point(data = pdx_cancelled_flights[which.min(pdx_cancelled_flights$num_cancelled_flights),], color = "blue", size = 3) +
  geom_text(data = pdx_cancelled_flights[which.min(pdx_cancelled_flights$num_cancelled_flights),], aes(label = num_cancelled_flights), nudge_x = 100) + 
  labs(title = "Number of cancelled flights at PDX airport, by airline",
       subtitle = "January 2020", 
       x = "Number of cancelled flights", 
       y = "Airline") + 
  theme_minimal()

```

