What issues will you address by cleaning the data?

I will address the issues of duplicated data, missing data, data in the wrong units,
data columns with the wrong data type, outliers, and NULL values.

Queries:
Below, provide the SQL queries you used to clean your data.

all_sessions Table:
1) Tables with only NULL values and irrelevant information were dropped.
    ```SQL
    ALTER TABLE all_sessions DROP "itemQuantity", DROP "itemRevenue", DROP "searchKeyword", DROP "transactionRevenue", DROP "productRefundAmount"
    ```
2) Added a new colonm of distinctly numbered rows to use as the Primary Key, ordered over session date.
    ```SQL
    SELECT ROW_NUMBER() OVER(ORDER BY date) AS session_id, * FROM all_sessions
    ```

3) All missing values were added in where possible, and all non-missing NULL values were converted to a more understandable value, this new table was then saved as a CTE, and then only the relevant columns were then selected and type casted to the correct type, and all dollar amounts were divided by 1000000 to get the correct dollar value to generate the clean table, the clean table was then saved as a view for use in analysis.
    ```SQL
    CREATE OR REPLACE VIEW all_sessions_cleaned AS
    WITH all_sessions_temp AS
    (
    SELECT ROW_NUMBER() OVER(ORDER BY date) AS session_id, *,
    CASE 
        WHEN transactions = '1' THEN transactions
        WHEN transactions IS NULL THEN '0'
        END AS transactions_filled,
    CASE
        WHEN ecommerceaction_option IS NOT NULL THEN ecommerceaction_option
        WHEN ecommerceaction_option IS NULL THEN 'None'
        END AS ecommerceaction_option_filled,
    CASE 
        WHEN "transactionId" IS NOT NULL THEN "transactionId"
        WHEN "transactionId" IS NULL THEN 'No Transaction'
        END AS transaction_id,
    CASE
        WHEN "sessionQualityDim" IS NOT NULL THEN "sessionQualityDim"
        WHEN "sessionQualityDim" IS NULL THEN '0'
        END AS session_quality_dim,
    CASE
        WHEN "transactionId" IS NULL THEN '0'
        ELSE "productRevenue"
        END AS product_revenue,
    CASE
        WHEN "transactionId" IS NULL THEN '0'
        ELSE "productQuantity"
        END AS product_quantity,
    CASE
        WHEN "transactionId" IS NULL THEN '0'
        ELSE "totalTransactionRevenue"
        END AS total_transaction_revenue,
    CASE
        WHEN "timeOnSite" IS NOT NULL THEN "timeOnSite"
        ELSE '0'
        END AS time_on_site,
    CASE
        WHEN "currencyCode" IS NOT NULL THEN "currencyCode"
        ELSE 'USD'
        END AS currency_code
    FROM all_sessions
)
SELECT session_id, "fullVisitorId", "channelGrouping", CAST(time AS INT), country, city, CAST(total_transaction_revenue AS FLOAT)/1000000 AS total_transaction_revenue,
CAST(transactions_filled AS INT), CAST(time_on_site AS INT), CAST(pageviews AS INT), CAST(session_quality_dim AS INT), CAST(date AS DATE), "visitId", type, CAST(product_quantity AS INT),
CAST("productPrice" AS FLOAT)/1000000 AS product_price, CAST(product_revenue AS FLOAT)/1000000 AS product_revenue, "productSKU", "v2ProductName", "v2ProductCategory", 
"productVariant", currency_code, transaction_id, "pageTitle", "pagePathLevel1", CAST("eCommerceAction_type" AS INT), CAST("eCommerceAction_step" AS INT),
ecommerceaction_option_filled
FROM all_sessions_temp
```

Analytics Table:

1) tables with only NULL values were dropped.
    ```SQL
    ALTER TABLE analytics DROP userid
    ```

2) Added a new colonm of distinctly numbered rows to use as the Primary Key, ordered over visitnumber.
    ```SQL
    SELECT ROW_NUMBER() OVER(ORDER BY visitnumber) AS id, * FROM analytics
    ```

3) All missing values were added in where possible, and all non-missing NULL values were converted to a more understandable value, all duplicate rows were identified by partitioning over the whole table, and then duplicates were eliminated by grouping each duplicate row together. this new table was then saved as a CTE, and then only the relevant columns were then selected and type casted to the correct type, and all dollar amounts were divided by 1000000 to get the correct dollar value to generate the clean table, the clean table was then saved as a view for use in analysis
    ```SQL
    CREATE OR REPLACE VIEW analytics_cleaned AS
    WITH analytics_temp AS
    (
    SELECT ROW_NUMBER() OVER(ORDER BY visitnumber) AS id, visitnumber, visitid, visitstarttime, date, fullvisitorid, channelgrouping, socialengagementtype, units_sold,
    pageviews, timeonsite, bounces, revenue, unit_price, ROW_NUMBER() OVER(PARTITION BY visitnumber, visitid, visitstarttime, date, fullvisitorid, channelgrouping, socialengagementtype, units_sold,
    pageviews, timeonsite, bounces, revenue, unit_price),
    CASE
        WHEN units_sold IS NULL THEN '0'
        ELSE units_sold
        END AS units_sold_filled,
    CASE
        WHEN bounces IS NULL THEN '0'
        ELSE bounces
        END AS bounces_filled,
    CASE
        WHEN units_sold IS NOT NULL THEN CAST(units_sold AS FLOAT) * CAST(unit_price AS FLOAT)
        ELSE 0
        END AS revenue_filled,
    CASE
        WHEN timeonsite IS NULL THEN '0'
        ELSE timeonsite
        END AS timeonsite_filled
    FROM analytics
    GROUP BY visitnumber, visitid, visitstarttime, date, fullvisitorid, channelgrouping, socialengagementtype, units_sold,
    pageviews, timeonsite, bounces, revenue, unit_price
    )
SELECT id, CAST(visitnumber AS INT), visitid, visitstarttime, CAST(date AS date), fullvisitorid, channelgrouping, socialengagementtype,
CAST(units_sold_filled AS INT), CAST(pageviews AS INT), CAST(timeonsite_filled AS INT), CAST(bounces_filled AS INT), CAST(revenue_filled AS FLOAT)/1000000 AS revenue,
CAST(unit_price AS FLOAT)/1000000 AS unit_price
FROM analytics_temp
```

Products Table:

1) sku column was renamed to productSKU to be consistent with other tables, also only a couple values were NULL in the table, so the NULL values were filled in with an understandable value, this table was saved as a CTE and then only the relevant columns were selected and type casted to the correct type to generate the clean table.
```SQL
    CREATE OR REPLACE VIEW products_cleaned AS
    WITH products_temp AS
    (
    SELECT *,
    CASE
        WHEN sentimentscore IS NULL THEN '0'
        ELSE sentimentscore
        END AS sentimentscore_filled,
    CASE
        WHEN sentimentmagnitude IS NULL THEN '0'
        ELSE sentimentmagnitude
        END AS sentimentmagnitude_filled
    FROM products
    )
SELECT "productSKU", name, CAST(orderedquantity AS INT), CAST(stocklevel AS INT), CAST(restockingleadtime AS INT),
CAST(sentimentscore_filled AS FLOAT), CAST(sentimentmagnitude_filled AS FLOAT)
FROM products_temp
```

Sales_by_sku Table:

1) Table is already clean, so only the productsku column was renamed to productSKU to be consistent with other tables and data types changed to correct ones.
```SQL
    ALTER TABLE sales_by_sku ALTER COLUMN total_ordered TYPE INT USING total_ordered::integer
```

Sales_report Table:

1) productsku colunm was renamed to productSKU to be consistent with other tables

2) Only the ratio colunm had NULL values, and it was because it is dividing by zero when the stocklevel is zero, so all the NULL values were replaced with 'no stock' for better understanding, this table was saved as a CTE and then only the relevant colunms were selected and type casted to the correct type to generate the clean table, the table was then saved as a view for use in analysis.
```SQL
    CREATE OR REPLACE VIEW sales_report_cleaned AS
    WITH sales_report_temp AS 
    (
    SELECT *,
    CASE
        WHEN stocklevel = '0' THEN 'no stock'
        ELSE ratio
        END AS ratio_filled
    FROM sales_report
    )    
SELECT "productSKU", CAST(total_ordered AS INT), name, CAST(stocklevel AS INT), CAST(restockingleadtime AS INT), CAST(sentimentscore AS FLOAT),
CAST(sentimentmagnitude AS FLOAT), ratio_filled
FROM sales_report_temp
```
