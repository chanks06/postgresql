# AIRPLANES 


## Part A

What is the name of the airport which, over the given month’s data, has the greatest
average delay in arriving flights? Limit your answer to only include airports with
at least 100 arriving flights over the course of the month.

**Answer: Aspen Pitkin County Sardy Field, avg_delay = 22.644 ** 
```sql
SELECT a.description, avg(f.arrive_delay) as avg_delay FROM hw6.flights as f 
JOIN hw6.airports as a
ON f.dest_airport_id = a.airport_id 
GROUP BY f.dest_airport_id, a.description
HAVING count(f.flight_number) >= 100
ORDER BY avg_delay DESC
LIMIT 1; 
```

## Part B

Which 3 US cities have the greatest number of inbound flights across all airports serving that city? (Some cities have multiple airports, which is what the
city_market_id is supposed to assist with.)

**Answer: New York City, NY, Atlanta, GA, Chicago, IL ** 
```sql
SELECT cm.description, count(f.dest_city_market_id) as num_of_flights_in_cm FROM hw6.flights as f 
JOIN hw6.city_markets as cm 
ON f.dest_city_market_id = cm.city_market_id
GROUP BY f.dest_city_market_id, cm.description
ORDER BY num_of_flights_in_cm DESC
LIMIT 3; 
```

## Part C

Which 5 airports have the greatest number of outgoing flights per day on average?
How many flights (on average) are leaving those airports each day?

**Answer: Hartsfield-Jackson Atlanta International: 1038 flights
		  Chicago O'Hare: 827 flights
		  Dallas/Fort Worth International: 785 flights
		  Denver International: 658 flights
		  Charlotte Douglas International: 645 flights 
```sql
SELECT a.description, (count(f.flight_number))/31 as avg_flights_per_day from hw6.flights as f 
JOIN hw6.airports as a 
ON f.origin_airport_id = a.airport_id 
GROUP BY a.description
ORDER BY avg_flights_per_day DESC
LIMIT 5; 
```

## Part D

For each major airline carrier, which two airports represent the endpoints of that
carrier’s longest flight (by distance)? There are 17 distinct carriers in the month
represented in the dataset, so your output table should also have 17 rows with
the name of the carrier and the name of both airports. Dealing with round trip
duplicates here can be a little tricky, so think about how you could reliably filter out
one of the directions. It doesn’t matter whether you choose PDX to SEA or SEA
to PDX (for instance), you just need one of the pairings for each carrier.

**Answer: see long_plane_trips.csv in github** 
```sql
SELECT DISTINCT carrier, a1.description as origin_airport , a2.description as destination_airport

FROM (

    SELECT 
        max(f1.flight_distance) as max_distance, f1.carrier_id as carrier_id, c.name as carrier FROM hw6.flights as f1
        JOIN hw6.carriers as c ON
        c.carrier_id = f1.carrier_id
        GROUP BY f1.carrier_id, c.name
        ) as t1

JOIN hw6.flights as f2 ON 
f2.carrier_id = t1.carrier_id
JOIN hw6.airports as a1 ON 
a1.airport_id = f2.origin_airport_id
JOIN hw6.airports as a2 ON
a2.airport_id = f2.dest_airport_id
WHERE max_distance = f2.flight_distance
AND f2.origin_airport_id > f2.dest_airport_id;
```

