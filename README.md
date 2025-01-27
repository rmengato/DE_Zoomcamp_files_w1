# DE_Zoomcamp_files_w1
Files for week 1 of DE Zoomcamp 2025 homework

DataTalks DE Zoomcamp Homework week 1

### Question 1. Version of pip
question 1 command:

on file location, run:
docker run --entrypoint bash -it python:3.12.8

and then:
pip --version


### Question 3. Trip Segmentation count
This and the subsequent questions required the loading of the datasets to the database through the script present in the file DE_zoomcamp_week1_homework.
They also required running the PostgreSQL database ny_taxi, through the command below:

docker run -it ^
-e POSTGRES_USER="root" ^
-e POSTGRES_PASSWORD="root" ^
-e POSTGRES_DB="ny_taxi" ^
-v C:/Users/rmeng/Desktop/de_zoomcamp/ny_taxi_postgres_data:/var/lib/postgresql/data ^
-p 5432:5432 ^
postgres:13

and thereafter the following commands for accessing the db with pgcli:

pgcli -h localhost -p 5432 -u root -d ny_taxi

query used for question 3:
select count(*) from green_taxi_data where trip_distance <= 1
+--------+
| count  |
|--------|
| 104838 |
+--------+
By exclusion, this result alone is able to answer the question.


### Question 4. Longest trip
select lpep_pickup_datetime from green_taxi_data order by trip_distance desc limit 5 //could be limit 1, which would be more specific
+----------------------+
| lpep_pickup_datetime |
|----------------------|
| 2019-10-31 23:23:41  |
| 2019-10-11 20:34:21  |
| 2019-10-26 03:02:39  |
| 2019-10-24 10:59:58  |
| 2019-10-05 16:42:04  |
+----------------------+

We can see that the longest trip took place in 2019-10-31.


### Question 5. Biggest pickup zones
with trips_location_cte as (select * from green_taxi_data as gtd left join zone_lookup_data as zld on gtd."PULocationID" = zld."LocationID") se
 lect "Zone", sum(total_amount) from trips_location_cte where date(lpep_pickup_datetime) = '2019-10-18'  group by "Zone" having sum(total_amount) > 13000 order by sum(
 total_amount) desc limit 5
 
+---------------------+--------------------+
| Zone                | sum                |
|---------------------+--------------------|
| East Harlem North   | 18686.679999999724 |
| East Harlem South   | 16797.25999999983  |
| Morningside Heights | 13029.789999999904 |
+---------------------+--------------------+

On the day 2019-10-18, these where the biggest pickup zones with 13000 

### Question 6. Largest tip
select DOLocationID from (with trips_location_cte as (select * from green_taxi_data as gtd left join zone_lookup_data as zld on gtd."PULocationID" = zld."LocationID") select trips_location_cte."Zone", trips_location_cte."DOLocationID", max(tip_amount) from trips_location_cte where "Zone" = 'East Harlem North' group by "Zone", "DOLocationID" order by max(tip_amount) desc limit 1)

+-------------------+--------------+------+
| Zone              | DOLocationID | max  |
|-------------------+--------------+------|
| East Harlem North | 132          | 87.3 |
+-------------------+--------------+------+

After running another query, the 132 ID stands for the JFK Airport.
