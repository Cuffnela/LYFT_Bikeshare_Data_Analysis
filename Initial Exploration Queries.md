## Part 1 - Querying Data with BigQuery

### Initial queries
?
Paste your SQL query and answer the question in a sentence.  Be sure you properly format your queries and results using markdown. 

- What's the size of this dataset? (i.e., how many trips)

```sql
SELECT count(*) FROM `bigquery-public-data.san_francisco.bikeshare_trips`
```
```
983648
```

- What is the earliest start date and time and latest end date and time for a trip?

```sql
SELECT min(start_date), max(end_date)
FROM `bigquery-public-data.san_francisco.bikeshare_trips`
```
```
2013-08-29 09:08:00
2016-08-31 23:48:00
```

- How many bikes are there?

```sql
SELECT count(distinct bike_number)
FROM `bigquery-public-data.san_francisco.bikeshare_trips`
```
```
700
```

### Questions of your own
- Make up 3 questions and answer them using the Bay Area Bike Share Trips Data.  These questions MUST be different than any of the questions and queries you ran above.

- Question 1: What are the different subscriber types?
  * Answer:
    ```
    Customer
    Subscriber
    ```
  * SQL query:
    ```sql
    SELECT distinct subscriber_type
    FROM `bigquery-public-data.san_francisco.bikeshare_trips`
    ```
- Question 2: How many trips start and end at the same station?
  * Answer:
  ```
  32047
  ```
  * SQL query:
  ```sql
  SELECT count(*)
  FROM `bigquery-public-data.san_francisco.bikeshare_trips`
  WHERE start_station_id = end_station_id;
  ```

- Question 3: What is the average trip length?
  * Answer:
  ```
  Row	AvgTripSec	AvgTripMin	
  1	1018.93 	 16.98

  ```
  * SQL query:
  ```sql
  SELECT TRUNC(AVG(duration_sec),2) AS AvgTripSec, TRUNC(AVG(duration_sec)/60,2) AS AvgTripMin
  FROM `bigquery-public-data.san_francisco.bikeshare_trips`;
  ```
---

## Part 2 - Querying data from the BigQuery CLI 

- Use BQ from the Linux command line:

  * General query structure

    ```
    bq query --use_legacy_sql=false '
        SELECT count(*)
        FROM
           `bigquery-public-data.san_francisco.bikeshare_trips`'
    ```

### Queries

1. Rerun the first 3 queries from Part 1 using bq command line tool (Paste your bq
   queries and results here, using properly formatted markdown):

  * What's the size of this dataset? (i.e., how many trips)
    ```
    bq query --use_legacy_sql=false 'SELECT count(*) AS total_trips FROM `bigquery-public-data.san_francisco.bikeshare_trips`'
    ```
    ```
    +--------------+
    |  total trips |
    +--------------+
    |    983648    |
    +--------------+
    ```
  * What is the earliest start time and latest end time for a trip?
  ```
  bq query --use_legacy_sql=false 'SELECT min(start_date) AS earliest, max(end_date) AS latest FROM `bigquery-public-data.san_francisco.bikeshare_trips`'
  ```
  ```
  +---------------------+---------------------+
  |       earliest      |        latest       |
  +---------------------+---------------------+
  | 2013-08-29 09:08:00 | 2016-08-31 23:48:00 |
  +---------------------+---------------------+
  ```

  * How many bikes are there?
  ```
  bq query --use_legacy_sql=false 'SELECT count(distinct bike_number) AS total_bikes FROM `bigquery-public-data.san_francisco.bikeshare_trips`'
  ```
  ```
  +--------------+
  | total_bikes  |
  +--------------+
  |    700       |
  +--------------+
  ```

2. New Query (Run using bq and paste your SQL query and answer the question in a sentence, using properly formatted markdown):

  * How many trips are in the morning vs in the afternoon?
  Trips classified as morning trips if they started after 5am but before noon. Trips were considered afternoon trips if they started between noon and 5pm. 
```
bq query --use_legacy_sql=false 'SELECT SUM(CASE WHEN (EXTRACT(HOU
R FROM start_date) >= 5 and EXTRACT(HOUR FROM start_date) < 12) then 1 END) AS Morning, SUM(CASE WHEN EXTRACT(HOUR 
FROM start_date) >= 12 and EXTRACT(HOUR FROM start_date) <= 17 then 1 END) AS Afternoon FROM `bigquery-public-data.
san_francisco.bikeshare_trips`'
```

```
+---------+-----------+
| Morning | Afternoon |
+---------+-----------+
|  404919 |    391199 |
+---------+-----------+
```

### Project Questions
Identify the main questions you'll need to answer to make recommendations (list
below, add as many questions as you need).

- Question 1: What day of the week and time of day is each of the trips in the data set?

- Question 2: What is a reasonable commuter trip length? What is the mean and standard deviation of trips by day?

- Question 3: Which trips start and end at different stations and are a reasonable communter length?

- Question 4: What are the low and peak rental times during the day?

- Question 5: Are any stations sold out during the day? Are any stations not renting out bikes?

- Question 6: What are the most popular and least popular stations?

- Question 7: What are the low and peak rental days during the week?

- Question 8: What percent of trips appear to be customers changing their mind and not riding? (i.e. renting and returning a bike in a very short window.)

- Question 9: Are low performing bike stations near eachother or near high performing bike stations?

### Answers

Answer at least 4 of the questions you identified above You can use either
BigQuery or the bq command line tool.  Paste your questions, queries and
answers below.

- Question 1: What day of the week and time of day is each of the trips in the data set?
  * Answer:(Print out limited to first 5 results.)
  ```
  Row	start_date          	dow_int	dow_str 	dow_weekday	start_hour	start_hour_str	end_date	
    1	2013-08-29 09:08:00 UTC	5   	Thursday	Weekday 	9       	Mid Morning	2013-08-29 09:11:00 UTC
    2	2013-08-29 09:24:00 UTC	5   	Thursday	Weekday 	9       	Mid Morning	2013-08-29 09:42:00 UTC
    3	2013-08-29 09:24:00 UTC	5   	Thursday	Weekday 	9       	Mid Morning	2013-08-29 09:43:00 UTC
    4	2013-08-29 09:24:00 UTC	5   	Thursday	Weekday 	9       	Mid Morning	2013-08-29 09:43:00 UTC
    5	2013-08-29 09:25:00 UTC	5   	Thursday	Weekday 	9       	Mid Morning	2013-08-29 09:43:00 UTC
 ```
  * SQL query:
  ``` sql
  SELECT 
       start_date,
       EXTRACT(DAYOFWEEK FROM start_date) AS dow_int,
       CASE EXTRACT(DAYOFWEEK FROM start_date)
           WHEN 1 THEN "Sunday"
           WHEN 2 THEN "Monday"
           WHEN 3 THEN "Tuesday"
           WHEN 4 THEN "Wednesday"
           WHEN 5 THEN "Thursday"
           WHEN 6 THEN "Friday"
           WHEN 7 THEN "Saturday"
           END AS dow_str,
       CASE 
           WHEN EXTRACT(DAYOFWEEK FROM start_date) IN (1, 7) THEN "Weekend"
           ELSE "Weekday"
           END AS dow_weekday,
       EXTRACT(HOUR FROM start_date) AS start_hour,
       CASE 
           WHEN EXTRACT(HOUR FROM start_date) < 5  OR EXTRACT(HOUR FROM start_date) >= 23 THEN "Nightime"
           WHEN EXTRACT(HOUR FROM start_date) >= 5 and EXTRACT(HOUR FROM start_date) <= 8 THEN "Morning"
           WHEN EXTRACT(HOUR FROM start_date) >= 9 and EXTRACT(HOUR FROM start_date) <= 10 THEN "Mid Morning"
           WHEN EXTRACT(HOUR FROM start_date) >= 11 and EXTRACT(HOUR FROM start_date) <= 12 THEN "Mid Day"
           WHEN EXTRACT(HOUR FROM start_date) >= 13 and EXTRACT(HOUR FROM start_date) <= 15 THEN "Afternoon"
           WHEN EXTRACT(HOUR FROM start_date) >= 16 and EXTRACT(HOUR FROM start_date) <= 19 THEN "Early Evening"
           WHEN EXTRACT(HOUR FROM start_date) >= 20 and EXTRACT(HOUR FROM start_date) <= 22 THEN "Evening"
           END AS start_hour_str,
    end_date,
FROM `bigquery-public-data.san_francisco.bikeshare_trips`
ORDER BY start_date ASC;
  ```

- Question 2: What is a reasonable commuter trip length? What is the mean and standard deviation of trips by day?
  * Answer:
  ```
  Row	Day_Of_Week 	avg_duration_min	stdev_duration_min	
    1	Saturday    	 13.6           	 9.87	
    2	Sunday      	 13.56          	 9.83	
    3	Friday      	 9.59           	 6.0	
    4	Thursday    	 9.34           	 5.53	
    5	Monday       	 9.28           	 5.47	
    6	Wednesday   	 9.24           	 5.37	
    7	 Tuesday    	 9.19           	 5.35	
   ```
  * SQL query:
  ```sql
  SELECT  dow_str AS Day_Of_Week, 
        TRUNC(AVG(duration_min),2) AS avg_duration_min,
        TRUNC(STDDEV_SAMP(duration_min),2) AS stdev_duration_min
    FROM `w205-cuffneylaurie.bike_trip_data.Valid_Trips` 
    GROUP BY dow_str
    ORDER BY avg_duration_min DESC;
  ```

- Question 3: Which trips start and end at different stations and are a reasonable communter length?
  * Answer:(Print out limited to first 5 results.)
  ```
  Row	start_date              	dow_int	dow_str 	dow_weekday	start_hour	start_hour_str	end_date                  	start_station_id	end_station_id	duration_sec	duration_min	trip_id	
    1	2016-02-14 05:25:00 UTC 	1   	Sunday   	Weekend 	5       	Morning       	2016-02-14 05:42:00 UTC 	26                  	83       	1021          	17.01      	1092841	
    2	2015-10-25 12:45:00 UTC 	1   	Sunday   	Weekend 	12      	Mid Day       	2015-10-25 12:54:00 UTC 	24                  	83       	536           	8.93     	984411	
    3	2015-10-25 12:45:00 UTC 	1   	Sunday   	Weekend 	12      	Mid Day       	2015-10-25 12:53:00 UTC 	24                  	83       	487           	8.11     	984412	
    4	2014-10-26 13:11:00 UTC 	1   	Sunday   	Weekend 	13      	Afternoon      	2014-10-26 13:21:00 UTC 	11                  	9       	566           	9.43     	515698	
    5	2016-06-12 18:15:00 UTC 	1   	Sunday   	Weekend 	18      	Early Evening	2016-06-12 18:26:00 UTC 	11                  	9       	627           	10.44      	1236584
  ```

  * SQL query:
  ```sql
  SELECT 
    start_date,
       EXTRACT(DAYOFWEEK FROM start_date) AS dow_int,
       CASE EXTRACT(DAYOFWEEK FROM start_date)
           WHEN 1 THEN "Sunday"
           WHEN 2 THEN "Monday"
           WHEN 3 THEN "Tuesday"
           WHEN 4 THEN "Wednesday"
           WHEN 5 THEN "Thursday"
           WHEN 6 THEN "Friday"
           WHEN 7 THEN "Saturday"
           END AS dow_str,
       CASE 
           WHEN EXTRACT(DAYOFWEEK FROM start_date) IN (1, 7) THEN "Weekend"
           ELSE "Weekday"
           END AS dow_weekday,
       EXTRACT(HOUR FROM start_date) AS start_hour,
       CASE 
           WHEN EXTRACT(HOUR FROM start_date) < 5  OR EXTRACT(HOUR FROM start_date) >= 23 THEN "Nightime"
           WHEN EXTRACT(HOUR FROM start_date) >= 5 and EXTRACT(HOUR FROM start_date) <= 8 THEN "Morning"
           WHEN EXTRACT(HOUR FROM start_date) >= 9 and EXTRACT(HOUR FROM start_date) <= 10 THEN "Mid Morning"
           WHEN EXTRACT(HOUR FROM start_date) >= 11 and EXTRACT(HOUR FROM start_date) <= 12 THEN "Mid Day"
           WHEN EXTRACT(HOUR FROM start_date) >= 13 and EXTRACT(HOUR FROM start_date) <= 15 THEN "Afternoon"
           WHEN EXTRACT(HOUR FROM start_date) >= 16 and EXTRACT(HOUR FROM start_date) <= 19 THEN "Early Evening"
           WHEN EXTRACT(HOUR FROM start_date) >= 20 and EXTRACT(HOUR FROM start_date) <= 22 THEN "Evening"
           END AS start_hour_str,
    end_date,
    start_station_id, end_station_id, duration_sec, TRUNC(duration_sec/60,2) AS duration_min, trip_id
    FROM `bigquery-public-data.san_francisco.bikeshare_trips`
    WHERE start_station_id != end_station_id AND (duration_sec<60*60 AND duration_sec>60*2);
  ```
  
- Question 4: What are the low and peak rental times during the day?
  * Answer:
  ```
  Row	Day_Of_Week	Time_Of_Day 	total_trips	
    1	Friday  	Early Evening	51581	
    2	Friday  	Morning     	37101	
    3	Friday  	Mid Morning  	22681	
    4	Friday  	Afternoon   	20144	
    5	Friday  	Mid Day     	12587   	
    6	Friday  	Evening     	5955   	
    7	Friday  	Nightime    	1982   	
    8	Monday  	Early Evening  	62268   	
    9	Monday  	Morning     	42441   	
    10	Monday  	Mid Morning   	23343   	
    11	Monday  	Afternoon   	15957   	
    12	Monday  	Mid Day     	10756   	
    13	Monday  	Evening     	7218   	
    14	Monday  	Nightime     	1227   	
    15	Saturday  	Early Evening  	13246   	
    16	Saturday  	Afternoon   	13194   	
    17	Saturday  	Mid Day     	8738   	
    18	Saturday  	Mid Morning   	6194   	
    19	Saturday  	Evening     	3957       	
    20	Saturday  	Morning     	2992       	
    21	Saturday  	Nightime    	2003   	
    22	Sunday  	Afternoon   	11562   	
    23	Sunday  	Early Evening  	11200   	
    24	Sunday  	Mid Day     	7371   	
    25	Sunday  	Mid Morning   	5166   	
    26	Sunday  	Evening      	2877   	
    27	Sunday  	Morning      	2004   	
    28	Sunday  	Nightime      	1538   	
    29	Thursday  	Early Evening   60403   	
    30	Thursday  	Morning      	43527   	
    31	Thursday  	Mid Morning   	24461   	
    32	Thursday  	Afternoon   	18514   	
    33	Thursday  	Mid Day      	13054   	
    34	Thursday  	Evening      	8246   	
    35	Thursday  	Nightime      	1876   	
    36	Tuesday  	Early Evening   65802   	
    37	Tuesday  	Morning      	47492   	
    38	Tuesday  	Mid Morning   	25000   	
    39	Tuesday  	Afternoon   	17350   	
    40	Tuesday  	Mid Day      	12444   	
    41	Tuesday  	Evening      	8437   	
    42	Tuesday  	Nightime      	1519   	
    43	Wednesday  	Early Evening  	63498   	
    44	Wednesday  	Morning      	45395   	
    45	Wednesday  	Mid Morning   	25121   	
    46	Wednesday  	Afternoon   	17568   	
    47	Wednesday  	Mid Day      	12248   	
    48	Wednesday  	Evening      	8808   	
    49	Wednesday  	Nightime      	1775   	
  ```
  * SQL query:
  ```sql
  SELECT  dow_str AS Day_Of_Week, start_hour_str AS Time_Of_Day,
        Count(trip_id) AS total_trips,
    FROM `w205-cuffneylaurie.bike_trip_data.Valid_Trips` 
    GROUP BY Day_Of_Week, Time_Of_Day
    ORDER BY Day_Of_Week, total_trips DESC;
    ```
  
- Question 5: Are any stations sold out during the day? Are any stations not renting out bikes?
  * Answer: (Print out limited to first 5 results.)
  ```
  Row	max_available	min_available	dow_str 	station_id	
    1	27          	0           	Friday  	2	
    2	27          	0           	Monday  	2	
    3	26          	0           	Saturday	2	
    4	27          	0           	Sunday  	2	
    5	27          	0           	Thursday	2	
  ```
  * SQL query:
    ```sql
    SELECT MAX(bikes_available) as max_available, MIN(bikes_available) as min_available, dow_str, station_id
    FROM `w205-cuffneylaurie.bike_trip_data.status_days` 
    GROUP BY station_id, dow_str
    ORDER BY station_id, dow_str;
    ```

---

