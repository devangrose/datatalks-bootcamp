### Setup

- Ran the provided script to download the parquet files
- Created an external table using the UI tools
- Ran the following to create the internal table
```
CREATE OR REPLACE `nytaxi.yellow_trip_data`
AS SELECT * FROM `nytaxi.external_yellow_trip_data`;
```

### Question 1

20,332,093 - found in the table details in the UI

### Question 2

Wrote this query
```
SELECT COUNT(DISTINCT PULocationID) FROM 
`nytaxi.external_yellow_trip_data`;
```

and hovered over the run button to see the estimated resources. 
Then I replaced the table name with the internal and noted the estimated resoures as well.


### Question 3

Googled BigQuery storage type and found it was columnar. Prior knowledge of columnar databases led me to the correct answer.

### Question 4

Ran the following query

```
SELECT COUNT(*) FROM 'nytaxi.external_yellow_trip_data'
WHERE fare_amount = 0;
```

### Question 5

Partitions are optimized for filters, and clusters are sorted by columns within those partitions

### Question 6

Wrote the query

```
SELECT COUNT(DISTINCT VendorID) 
FROM `nytaxi.yellow_trip_data`
WHERE TIMESTAMP_TRUNC(tpep_dropoff_datetime, DAY) >= TIMESTAMP("2024-03-01")
AND TIMESTAMP_TRUNC(tpep_dropoff_datetime, DAY) <= TIMESTAMP("2024-03-15")
```

and noted the estimated processing. 
Then change the table to `nytaxi.optimized_yellow_trip_data` and reran.


### Question 7

Looked up documentation of external tables in BigQuery and they're stored at the datasource, in this case its the Bucket

### Question 8

No - clustering is not always recommended, it provides a optimized queries for certain cases but if your queries do not fall within those then it either provides no benefit or is detrimental to efficiency. 