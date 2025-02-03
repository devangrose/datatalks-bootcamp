
### Setup
- Ran scripts 4 and 5
- Created this yaml to ingest all the data
```
id: homework_2
namespace: zoomcamp

inputs: 
  - id: taxi
    type: SELECT
    displayName: Select taxi type
    values: [yellow, green]
    defaults: green
tasks:
  - id: year
    type: io.kestra.plugin.core.flow.ForEach
    values: ["2019", "2020", "2021"]
    concurrencyLimit: 1
    tasks:
      - id: month
        type: io.kestra.plugin.core.flow.ForEach
        values: ["01", "02", "03", "04", "05", "06", "07", "08", "09", "10", "11", "12"]
        concurrencyLimit: 1
        tasks:
          - id: log
            type: io.kestra.plugin.core.log.Log
            message: "{{parent}}"
          - id: taxi_type
            type: io.kestra.plugin.core.flow.Subflow
            flowId: 06_gcp_taxi
            namespace: zoomcamp
            inputs: 
              taxi: "{{ inputs.taxi }}"
              year: "{{ parent.taskrun.value }}"
              month: "{{ taskrun.value }}"
```
- Ran it for both options to ingest all the data

### Question 1

Looked for size of yellow_tripdata_2020-12.csv in GCS bucket OR check outputs tab in kestra.

### Question 2

Used my head simple string interpolation - green_tripdata_2020-04.csv

### Question 3

Ran in big query:
```
SELECT COUNT(1) 
FROM `datatalksbootcamp-448117.zoomcamp.yellow_tripdata` 
WHERE TIMESTAMP_TRUNC(tpep_pickup_datetime, DAY) >= TIMESTAMP("2020-01-01")
AND TIMESTAMP_TRUNC(tpep_dropoff_datetime, DAY) <
TIMESTAMP("2021-01-01")
```
### Question 4

Ran in big query: 
```
SELECT COUNT(1) 
FROM `datatalksbootcamp-448117.zoomcamp.green_tripdata` 
WHERE TIMESTAMP_TRUNC(lpep_pickup_datetime, DAY) >= TIMESTAMP("2020-01-01")
AND TIMESTAMP_TRUNC(lpep_dropoff_datetime, DAY) <
TIMESTAMP("2021-01-01")
```

### Question 5
Executed ` 06_gcp_taxi` with Yellow, 2021, and 03 as inputs

Executed in big query:
```
SELECT COUNT(1) 
FROM `datatalksbootcamp-448117.zoomcamp.yellow_tripdata` 
WHERE TIMESTAMP_TRUNC(tpep_pickup_datetime, DAY) >= TIMESTAMP("2020-03-01")
AND TIMESTAMP_TRUNC(tpep_dropoff_datetime, DAY) <
TIMESTAMP("2021-04-01")
```

### Question 6

Looked up documentation for io.kestra.plugin.core.trigger.Schedule. Described time zone format. 