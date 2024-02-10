# dez2024-hw3
DataTalks.club - Data Engineering Zoomcamp 2024 homework#3

### Setup

First steps 

1. Created a bucket in Google Cloud Storage.
2. Downloaded each parquet file from `https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page` for 2022. 
3. Uploaded all the files (each file is a month of 2022) to the newly created bucket.

Create an external table using the Green Taxi Trip Records Data for 2022.

4. On Google Cloud BigQuery `Create a SQL query`.
5. Create the external table using this statement

```SQL
CREATE OR REPLACE EXTERNAL TABLE `dez-2024.nyc_taxi.green_tripdata_2022_external`
OPTIONS(
  format = 'PARQUET',
  uris = ['gs://dez2024_cr_hw3_bucket/green_tripdata_2022-*.parquet']
);
```

Create a table in BQ using the Green Taxi Trip Records for 2022 (do not partition or cluster this table).

4. On Google Cloud BigQuery create a proyect
5. In that proyect create a dataset with name `nyc_taxi`
6. Then using the wizzard create a new table using the bucket name patter to load all the data from all files in the new Table. the pattern is `dez2024_cr_hw3_bucket/green_tripdata_2022-*.parquet`

# Questions answers

### Q1- What is count of records for the 2022 Green Taxi Data?

This query resturns the result

```SQL
select count(*) from `dez-2024.nyc_taxi.green_tripdata_2022_external`;
```

### Q2 - What is the estimated amount of data that will be read when this query is executed on the External Table and the Table?

By highlighting this `two` queries you can get the answer on top right corner

```SQL
-- table
select count(distinct PULocationID)
from `dez-2024.nyc_taxi.green_tripdata_2022`
;
```

```SQL
-- external table
select count(distinct PULocationID)
from `dez-2024.nyc_taxi.green_tripdata_2022_external`
;
```

### Q3 - How many records have a fare_amount of 0?

This query returns the result

```SQL
select count(*)
from `dez-2024.nyc_taxi.green_tripdata_2022_external`
where fare_amount = 0
;
```
### Q4 - What is the best strategy to make an optimized table in Big Query if your query will always order the results by PUlocationID and filter based on lpep_pickup_datetime?

Even If we try to partition by 2 columns, BigQuery has a restrinction for only allowing to partition by 1 column. So the answer would be given by this creatin statement:

```SQL
-- Create table with partition and cluster
CREATE OR REPLACE TABLE `dez-2024.nyc_taxi.green_tripdata_2022_partitioned_clustered`
PARTITION BY 
  DATE(lpep_pickup_datetime) 
CLUSTER BY PUlocationID AS 
SELECT * FROM `dez-2024.nyc_taxi.green_tripdata_2022`
```

And to compare the bytes used, just highlight this 2 queries separately:

```SQL
-- regular table
select count(*)
from `dez-2024.nyc_taxi.green_tripdata_2022`
where date(lpep_pickup_datetime) between '2022-01-01' and '2022-04-15'
```

```SQL
-- partitioned and clustered table
select count(*)
from `dez-2024.nyc_taxi.green_tripdata_2022_partitioned_clustered`
where date(lpep_pickup_datetime) between '2022-01-01' and '2022-04-15'
```

### Q5 - What are these values?

By hightlighting this 2 queries, you can get the answer

```SQL
-- non-partitioned table
select max(lpep_pickup_datetime) as ma, count(distinct PULocationID) cant
from `dez-2024.nyc_taxi.green_tripdata_2022`
where date(lpep_pickup_datetime) between '2022-06-01' and '2022-06-30'
```

```SQL
-- partitioned table

```