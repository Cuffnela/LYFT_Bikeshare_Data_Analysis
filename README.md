# Project 1: Query Project

- In the Query Project, you will get practice with SQL while learning about
  Google Cloud Platform (GCP) and BiqQuery. You'll answer business-driven
  questions using public datasets housed in GCP. To give you experience with
  different ways to use those datasets, you will use the web UI (BiqQuery) and
  the command-line tools, and work with them in Jupyter Notebooks.

#### Problem Statement

- You're a data scientist at Lyft Bay Wheels (https://www.lyft.com/bikes/bay-wheels), formerly known as Ford GoBike, the
  company running Bay Area Bikeshare. You are trying to increase ridership, and
  you want to offer deals through the mobile app to do so. 
  
- What deals do you offer though? Currently, your company has several options which can change over time.  Please visit the website to see the current offers and other marketing information. Frequent offers include: 
  * Single Ride 
  * Monthly Membership
  * Annual Membership
  * Bike Share for All
  * Access Pass
  * Corporate Membership
  * etc.

- Through this project, you will answer these questions: 

  * What are the 5 most popular trips that you would call "commuter trips"? 
  
  * What are your recommendations for offers (justify based on your findings)?

- Please note that there are no exact answers to the above questions, just like in the proverbial real world.  This is not a simple exercise where each question above will have a simple SQL query. It is an exercise in analytics over inexact and dirty data. 

- You won't find a column in a table labeled "commuter trip".  You will find you need to do quite a bit of data exploration using SQL queries to determine your own definition of a communter trip.  In data exploration process, you will find a lot of dirty data, that you will need to either clean or filter out. You will then write SQL queries to find the communter trips.

- Likewise to make your recommendations, you will need to do data exploration, cleaning or filtering dirty data, etc. to come up with the final queries that will give you the supporting data for your recommendations. You can make any recommendations regarding the offers, including, but not limited to: 
  * market offers differently to generate more revenue 
  * remove offers that are not working 
  * modify exising offers to generate more revenue
  * create new offers for hidden business opportunities you have found
  * etc. 

#### All Work MUST be done in the Google Cloud Platform (GCP) / The Majority of Work MUST be done using BigQuery SQL / Usage of Temporary Tables, Views, Pandas, Data Visualizations

A couple of the goals of w205 are for students to learn how to work in a cloud environment (such as GCP) and how to use SQL against a big data data platform (such as Google BigQuery).  In keeping with these goals, please do all of your work in GCP, and the majority of your analytics work using BigQuery SQL queries.

You can make intermediate temporary tables or views in your own dataset in BigQuery as you like.  Actually, this is a great way to work!  These make data exploration much easier.  It's much easier when you have made temporary tables or views with only clean data, filtered rows, filtered columns, new columns, summary data, etc.  If you use intermediate temporary tables or views, you should include the SQL used to create these, along with a brief note mentioning that you used the temporary table or view.

In the final Jupyter Notebook, the results of your BigQuery SQL will be read into Pandas, where you will use the skills you learned in the Python class to print formatted Pandas tables, simple data visualizations using Seaborn / Matplotlib, etc.  You can use Pandas for simple transformations, but please remember the bulk of work should be done using Google BigQuery SQL.

#### GitHub Procedures

In your Python class you used GitHub, with a single repo for all assignments, where you committed without doing a pull request.  In this class, we will try to mimic the real world more closely, so our procedures will be enhanced. 

Each project, including this one, will have it's own repo.

Important:  In w205, please never merge your assignment branch to the master branch. 

Using the git command line: clone down the repo, leave the master branch untouched, create an assignment branch, and move to that branch:
- Open a linux command line to your virtual machine and be sure you are logged in as jupyter.
- Create a ~/w205 directory if it does not already exist `mkdir ~/w205`
- Change directory into the ~/w205 directory `cd ~/w205`
- Clone down your repo `git clone <https url for your repo>`
- Change directory into the repo `cd <repo name>`
- Create an assignment branch `git branch assignment`
- Checkout the assignment branch `git checkout assignment`

The previous steps only need to be done once.  Once you your clone is on the assignment branch it will remain on that branch unless you checkout another branch.

The project workflow follows this pattern, which may be repeated as many times as needed.  In fact it's best to do this frequently as it saves your work into GitHub in case your virtual machine becomes corrupt:
- Make changes to existing files as needed.
- Add new files as needed
- Stage modified files `git add <filename>`
- Commit staged files `git commit -m "<meaningful comment about your changes>"`
- Push the commit on your assignment branch from your clone to GitHub `git push origin assignment`

Once you are done, go to the GitHub web interface and create a pull request comparing the assignment branch to the master branch.  Add your instructor, and only your instructor, as the reviewer.  The date and time stamp of the pull request is considered the submission time for late penalties. 

If you decide to make more changes after you have created a pull request, you can simply close the pull request (without merge!), make more changes, stage, commit, push, and create a final pull request when you are done.  Note that the last data and time stamp of the last pull request will be considered the submission time for late penalties.

Make sure you receive the emails related to your repository! Your project feedback will be given as comment on the pull request. When you receive the feedback, you can address problems or simply comment that you have read the feedback. 
AFTER receiving and answering the feedback, merge you PR to master. Your project only counts as complete once this is done.

---

## Parts 1, 2, 3

We have broken down this project into 3 parts, about 1 week's work each to help you stay on track.

**You will only turn in the project once at the end of part 3!**

- In Part 1, we will query using the Google BigQuery GUI interface in the cloud.

- In Part 2, we will query using the Linux command line from our virtual machine in the cloud.

- In Part 3, we will query from a Jupyter Notebook in our virtual machine in the cloud, save the results into Pandas, and present a report enhanced by Pandas output tables and simple data visualizations using Seaborn / Matplotlib.

---

## Part 1 - Querying Data with BigQuery

### SQL Tutorial

Please go through this SQL tutorial to help you learn the basics of SQL to help you complete this project.

SQL tutorial: https://www.w3schools.com/sql/default.asp

### Google Cloud Helpful Links

Read: https://cloud.google.com/docs/overview/

BigQuery: https://cloud.google.com/bigquery/

Public Datasets: Bring up your Google BigQuery console, open the menu for the public datasets, and navigate to the the dataset san_francisco.

- The Bay Bike Share has two datasets: a static one and a dynamic one.  The static one covers an historic period of about 3 years.  The dynamic one updates every 10 minutes or so.  THE STATIC ONE IS THE ONE WE WILL USE IN CLASS AND IN THE PROJECT. The reason is that is much easier to learn SQL against a static target instead of a moving target.

- (USE THESE TABLES!) The static tables we will be using in this class are in the dataset **san_francisco** :

  * bikeshare_stations

  * bikeshare_status

  * bikeshare_trips

- The dynamic tables are found in the dataset **san_francisco_bikeshare**

### Some initial queries

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

### Bonus activity queries (optional - not graded - just this section is optional, all other sections are required)

The bike share dynamic dataset offers multiple tables that can be joined to learn more interesting facts about the bike share business across all regions. These advanced queries are designed to challenge you to explore the other tables, using only the available metadata to create views that give you a broader understanding of the overall volumes across the regions(each region has multiple stations)

We can create a temporary table or view against the dynamic dataset to join to our static dataset.

Here is some SQL to pull the region_id and station_id from the dynamic dataset.  You can save the results of this query to a temporary table or view.  You can then join the static tables to this table or view to find the region:
```sql
#standardSQL
select distinct region_id, station_id
from `bigquery-public-data.san_francisco_bikeshare.bikeshare_station_info`
```

- Top 5 popular station pairs in each region

- Top 3 most popular regions(stations belong within 1 region)

- Total trips for each short station name in each region

- What are the top 10 used bikes in each of the top 3 region. these bikes could be in need of more frequent maintenance.

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

## Part 3 - Employ notebooks to synthesize query project results

### Get Going

Create a Jupyter Notebook against a Python 3 kernel named Project_1.ipynb in the assignment branch of your repo.

#### Run queries in the notebook 

At the end of this document is an example Jupyter Notebook you can take a look at and run.  

You can run queries using the "bang" command to shell out, such as this:

```
! bq query --use_legacy_sql=FALSE '<your-query-here>'
```

- NOTE: 
- Queries that return over 16K rows will not run this way, 
- Run groupbys etc in the bq web interface and save that as a table in BQ. 
- Max rows is defaulted to 100, use the command line parameter `--max_rows=1000000` to make it larger
- Query those tables the same way as in `example.ipynb`

Or you can use the magic commands, such as this:

```sql
%%bigquery my_panda_data_frame

select start_station_name, end_station_name
from `bigquery-public-data.san_francisco.bikeshare_trips`
where start_station_name <> end_station_name
limit 10
```

```python
my_panda_data_frame
```

#### Report in the form of the Jupter Notebook named Project_1.ipynb

- Using markdown cells, MUST definitively state and answer the two project questions:

  * What are the 5 most popular trips that you would call "commuter trips"? 
  
  * What are your recommendations for offers (justify based on your findings)?

- For any temporary tables (or views) that you created, include the SQL in markdown cells

- Use code cells for SQL you ran to load into Pandas, either using the !bq or the magic commands

- Use code cells to create Pandas formatted output tables (at least 3) to present or support your findings

- Use code cells to create simple data visualizations using Seaborn / Matplotlib (at least 2) to present or support your findings

### Resource: see example .ipynb file 

[Example Notebook](example.ipynb)

