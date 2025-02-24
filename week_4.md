### Question 1

subbed values based on defaults/overries

### Question 2

`var` should be outermost with a call to `env_var` nested inside, with a reference to a default inside of that

### Quesiton 3

the staging command only builds the staging and their children 

### Question 4

```
{{
    config(
        materialized='table'
    )
}}

WITH quarterly_revenue AS (
    SELECT 
        service_type,
        year,
        quarter,
        year_quarter,
        SUM(total_amount) as quarterly_revenue
    FROM {{ ref('fact_trips') }}
    GROUP BY service_type, year, quarter, year_quarter
),

-- Add previous year's data for YoY calculation
with_previous_year AS (
    SELECT 
        current_qtr.service_type,
        current_qtr.year,
        current_qtr.quarter,
        current_qtr.year_quarter,
        current_qtr.quarterly_revenue as current_revenue,
        prev_qtr.quarterly_revenue as previous_year_revenue,
        CASE 
            WHEN prev_qtr.quarterly_revenue IS NOT NULL AND prev_qtr.quarterly_revenue != 0
            THEN ROUND(
                ((current_qtr.quarterly_revenue - prev_qtr.quarterly_revenue) / prev_qtr.quarterly_revenue) * 100,
                2
            )
            ELSE NULL
        END as yoy_growth
    FROM quarterly_revenue current_qtr
    LEFT JOIN quarterly_revenue prev_qtr
        ON current_qtr.service_type = prev_qtr.service_type
        AND current_qtr.quarter = prev_qtr.quarter
        AND current_qtr.year = prev_qtr.year + 1
)

SELECT 
    service_type,
    year,
    quarter,
    year_quarter,
    current_revenue,
    previous_year_revenue,
    yoy_growth
FROM with_previous_year
ORDER BY service_type, year, quarter
```

