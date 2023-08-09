Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
```SQL
SELECT country, SUM(total_transaction_revenue) AS total_amount_transacted FROM all_sessions_cleaned
GROUP BY country
ORDER BY total_amount_transacted DESC
```
```SQL
SELECT city, SUM(total_transaction_revenue) AS total_amount_transacted FROM all_sessions_cleaned
GROUP BY city
ORDER BY total_amount_transacted DESC
```

Answer:
United States and Australia have the highest level of transaction revenue for countries. And "not avaliable in demo dataset", "Sunnyvale", "Sydney", "Austin",
and "Mountain View" have the highest level of transaction revenue for cities.



**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
```SQL
SELECT al.country, AVG(s.total_ordered) AS avg_amount_ordered FROM all_sessions_cleaned AS al
JOIN sales_by_sku AS s USING("productSKU")
GROUP BY al.country
ORDER BY avg_amount_ordered DESC
```
```SQL
SELECT al.city, AVG(s.total_ordered) AS avg_amount_ordered FROM all_sessions_cleaned AS al
JOIN sales_by_sku AS s USING("productSKU")
GROUP BY al.city
ORDER BY avg_amount_ordered DESC
```
Answer:
The city with the highest average number of products ordered from it is Brno with 319, and the country with the highest number is Saudi Arabia, with 96.3.




**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
```SQL
SELECT al.country, "v2ProductCategory", total_ordered FROM all_sessions_cleaned AS al
JOIN sales_by_sku AS s USING("productSKU")
GROUP BY al.country, "v2ProductCategory", total_ordered
ORDER BY country, total_ordered DESC
```
```SQL
SELECT al.city, "v2ProductCategory", total_ordered FROM all_sessions_cleaned AS al
JOIN sales_by_sku AS s USING("productSKU")
GROUP BY al.city, "v2ProductCategory", total_ordered
ORDER BY country, total_ordered DESC
```
Answer:
We can see that the most orders from countries like the United States, Croatia, India, and Germany come form the Home/Office category, we can also see that the most orders from cirites like Boston, Los Angeles, and New York also come from the Home/Office category. 






**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
```SQL
SELECT al.country, "v2ProductName", total_ordered FROM all_sessions_cleaned AS al
JOIN sales_by_sku AS s USING("productSKU")
GROUP BY al.country, "v2ProductName", total_ordered
ORDER BY al.country, total_ordered DESC 
```
```SQL
SELECT al.city, "v2ProductName", total_ordered FROM all_sessions_cleaned AS al
JOIN sales_by_sku AS s USING("productSKU")
GROUP BY al.city, "v2ProductName", total_ordered
ORDER BY al.city, total_ordered DESC 
```
```SQL
SELECT al.country, "v2ProductName", total_ordered FROM all_sessions_cleaned AS al
JOIN sales_by_sku AS s USING("productSKU")
GROUP BY al.country, "v2ProductName", total_ordered
ORDER BY total_ordered DESC 
```
```SQL
SELECT al.city, "v2ProductName", total_ordered FROM all_sessions_cleaned AS al
JOIN sales_by_sku AS s USING("productSKU")
GROUP BY al.city, "v2ProductName", total_ordered
ORDER BY total_ordered DESC 
```

Answer:
We can see that the top-selling product from a country like Albania is the "22 oz YouTube Bottle Infuser", and the top-selling product from a city like Amsterdam is the "YouTube Hard Cover Journal". With the last 2 queries we can also see that the "Ballpoint LED Light Pen" is the most ordered item per country and per city.




**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:
```SQL
SELECT al.country, SUM((s.total_ordered * al.product_price)) AS revenue FROM all_sessions_cleaned AS al
JOIN sales_by_sku AS s USING("productSKU")
GROUP BY al.country
ORDER BY revenue DESC  
```
```SQL
SELECT al.city, SUM((s.total_ordered * al.product_price)) AS revenue FROM all_sessions_cleaned AS al
JOIN sales_by_sku AS s USING("productSKU")
GROUP BY al.city
ORDER BY revenue DESC 
```


Answer:
We can see that the United States had the most impact on revenue generated from countries, over 2x as much as the 2nd highest impact which is the United Kingdom, where as Iceland and Malta have the least impact on revenue with 0 each. We can also see that the city "not available in demo dataset" had the most impact on revenue, and multiple cities have no impact with 0. 






