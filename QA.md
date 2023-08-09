What are your risk areas? Identify and describe them.
The productSKU values in the cleaned products, sales_by_sku, and sales_report might not be unique, and there could be errors in the data where a transaction was logged without a transaction id in the cleaned all_sessions table.


QA Process:
Describe your QA process and include the SQL queries used to execute it.

1) Checking the products, sales_by_sku, and sales_report tables to see if there are any repeating productSKU values.
```SQL
WITH products_duplicate_count AS
(
SELECT ROW_NUMBER() OVER(PARTITION BY "productSKU") AS duplicate_count FROM products_cleaned
)
SELECT * FROM products_duplicate_count
WHERE duplicate_count > 1
```
```SQL
WITH sales_duplicate_count AS
(
SELECT ROW_NUMBER() OVER(PARTITION BY "productSKU") AS duplicate_count FROM sales_by_sku
)
SELECT * FROM sales_duplicate_count
WHERE duplicate_count > 1
```
```SQL
WITH sales_duplicate_count AS
(
SELECT ROW_NUMBER() OVER(PARTITION BY "productSKU") AS duplicate_count FROM sales_by_sku
)
SELECT * FROM sales_duplicate_count
WHERE duplicate_count > 1
```
These 3 queries all result in 0 rows (as having a duplicate_count > 1 means its a duplicate value), meaning that there are no duplicate productSKU values

2) Checking the all_sessions table to see if there are any transactions that were logged but have no transaction id.
```SQL
SELECT COUNT(*) FROM all_sessions_cleaned
WHERE transaction_id LIKE 'No%' AND transactions_filled !=0
```
This query shows that there are actually 72 rows where there is "No transaction" but has a transaction that was filled, showing errors in this table. If i had more time, i would go back and try to figure out why this error exists, and whether these are outliers that should be dropped from the table.

