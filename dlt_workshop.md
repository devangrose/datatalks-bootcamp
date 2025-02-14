### Question 1

ran pip install and saw the number

### Question 2

Here is my code:
```
import dlt
from dlt.sources.helpers.rest_client import RESTClient
from dlt.sources.helpers.rest_client.paginators import PageNumberPaginator
import duckdb


# https://us-central1-dlthub-analytics.cloudfunctions.net/data_engineering_zoomcamp_api
# your code is here
@dlt.resource(name="rides")
def paginated_getter():
    client = RESTClient(
        base_url="https://us-central1-dlthub-analytics.cloudfunctions.net",
        paginator=PageNumberPaginator(base_page=1, total_path=None),
    )

    for page in client.paginate("data_engineering_zoomcamp_api"):
        yield page


# for page in paginated_getter():
#     print(page)
#     break


pipeline = dlt.pipeline(
    pipeline_name="ny_taxi_pipeline",
    destination="duckdb",
    dataset_name="ny_taxi_data",
)

# info = pipeline.run(paginated_getter(), write_disposition="append")

# print("Pipeline finished")
# print(info)

# A database '<pipeline_name>.duckdb' was created in working directory so just connect to it

# Connect to the DuckDB database
conn = duckdb.connect(f"{pipeline.pipeline_name}.duckdb")

# # Set search path to the dataset
conn.sql(f"SET search_path = '{pipeline.dataset_name}'")

# Describe the dataset
print(conn.sql("DESCRIBE"))
```

### Question 3

Ran this:
```
conn.sql("SELECT COUNT(*) FROM rides").show()
```

### Question 4

Ran the provided code