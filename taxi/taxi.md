# TAXI TAXI! 

The data for this problem is based on all the yellow taxi rides that took place within New York City over the month of January, 2022. Once you’ve downloaded the data,
you’ll need to begin by creating a new table named taxi_rides and populating it with the necessary columns and corresponding data types. The provided (and official!) data dictionary should help here with getting column data types correct, though you will probably still want to look at the CSV itself. In particular, note that the columns in the
CSV are not entirely in the same order as provided in the data dictionary. Additionally,the first column in the CSV is not mentioned in the data dictionary, and is just a unique integer assigned to each ride to be able to uniquely identify them.

As usual, you have space to fill in both your answer and the accompanying SQL statement (between the triple backtick regions). If an answer necessitated more that just reading a number off your table, please provide a short description of how you found your answer.

## Part A

How many records of taxi rides are included in the data set? How many records
are included which both started and ended outside of the supposed January 2022
time span? 

- **Answers: 2,463,931 records of taxi rides in this data set. 40 records are include that start and end outside of January 2022. **
```sql
--A.1
SELECT count(*)
FROM taxi_rides; 

--A.2
SELECT * FROM taxi_rides 
WHERE (tpep_pickup_datetime < '2022-01-01 00:00:00' OR tpep_pickup_datetime >= '2022-02-01 00:00:00') 
AND (tpep_dropoff_datetime < '2022-01-01 00:00:00' OR tpep_dropoff_datetime >= '2022-02-01 00:00:00');
```

## Part B

How many total passengers rode in taxis that traveled over toll roads?

- **Answer: 188,797 passengers **
```sql
SELECT sum(passenger_count) FROM taxi_rides 
WHERE tolls_amount > 0; 
```

## Part C

What is the most common number of passengers on a ride, and how many of these
types of rides occured within the dataset?

- **Answers: 1 passenger was most common passenger count, with 1,794,055 rides **
```sql
SELECT mode() WITHIN GROUP (ORDER BY passenger_count)
FROM taxi_rides; 

SELECT count(passenger_count) FROM taxi_rides
WHERE passenger_count = 1;

SELECT 10673 / 2463931::numeric;  
```

## Part D

What percentage of the total trips had a disputed charge?

- **Answer: .43% of rides had a disputed charge. I found this in 3 queries. 1) number of taxi rides with disputed payment, 10673. 2) total rides: 2,463,931 3) proportion of disputed rides over total rides, cast as a numeric (otherwise if computed as integers, output would be 0)**

```sql

SELECT 
count(*) FROM taxi_rides
WHERE payment_type = 4;

SELECT count(*) FROM taxi_rides; 

SELECT 10673 / 2463931::numeric; 

```

## Part E

For those riders that pay with a credit card, what is the average credit tip that is
left for the driver?

- **Answer: $3.02 average tip from riders who pay with a credit card **
```sql
SELECT avg(tip_amount) FROM taxi_rides 
WHERE payment_type = 1;
```

## Part F 

What was the median amount charged (total) per passenger across all rides?

- **Answer: $12.95**
```sql
SELECT percentile_cont(0.5) WITHIN GROUP (ORDER BY cost_per_passenger) 
    FROM (SELECT (total_amount / passenger_count) AS cost_per_passenger 
    FROM taxi_rides
    WHERE passenger_count != 0) AS cost_per_passenger;   

```

## Part G

What is the most common pickup location? Dropoff location? What about the
most common route (pair of pickup + dropoff locations)?

- **Answers: Most common pick-up location: 237, Upper East Side South, Manhattan
			 Most common drop-off location 236, Upper East Side North, Manhattan
			 Most common route: (237,236) Upper East Side South to North, Manhattan 
			 **
```sql 
--G.1
SELECT mode() WITHIN GROUP (ORDER BY pu_location_id)
    FROM taxi_rides; 

--G.2
SELECT mode() WITHIN GROUP (ORDER BY do_location_id)
    FROM taxi_rides; 

--G.3
SELECT mode() WITHIN GROUP (ORDER BY (pu_location_id, do_location_id))
    FROM taxi_rides; 
```

## Part H

How many taxi rides seemingly took their passengers back
in time? What was the average duration that they traveled back in time?

- **Answers: 1353 time travelers, with an average duration of 2 minutes, 58 seconds, 714.708 ms traveled back in time**

```sql
--H.1
SELECT * FROM taxi_rides
WHERE tpep_pickup_datetime > tpep_dropoff_datetime;

--H.2
SELECT avg(tpep_pickup_datetime - tpep_dropoff_datetime) FROM taxi_rides
WHERE tpep_pickup_datetime > tpep_dropoff_datetime;

```

