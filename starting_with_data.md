Question 1: 
What is the total number of unique visitors that visited the site through organic search?

SQL Queries:
```SQL
SELECT COUNT(DISTINCT("fullVisitorId")) FROM all_sessions_cleaned
WHERE "channelGrouping" LIKE 'Organic%'
```
Answer: 
There are 8207 unique visitors that visited the site through organic search.


Question 2: 
What is the lowest amount of pages viewed by a visitor that made a purchase on the website?

SQL Queries:
```SQL
SELECT an.pageviews, an.units_sold_filled FROM all_sessions_cleaned AS al
JOIN analytics_cleaned AS an ON al."fullVisitorId" = an.fullvisitorid
WHERE an.units_sold_filled != 0
ORDER BY an.pageviews
```
Answer:
The lowest amount of page views that made a purchase was 1.


Question 3: 
What is the average amount of pages viewed by a visitor that made more than one purchase on the website?

SQL Queries:
```SQL
SELECT ROUND(AVG(an.pageviews)) AS avg_page_views FROM all_sessions_cleaned AS al
JOIN analytics_cleaned AS an ON al."fullVisitorId" = an.fullvisitorid
WHERE an.units_sold_filled > 1
```
Answer:
The average amount of pages viewed by a visitor that made more than one purchses on the website was 32


