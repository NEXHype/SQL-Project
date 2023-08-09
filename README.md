# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
To create, import, and clean the given dataset, use the cleaned data tables to answer questions and solve problems, and to do quality assurance checks on the results.

## Process
### Created and imported the given data tables into the ecommerce database.
### Cleaned the given data tables by removing duplicate rows, dropping irrelevant coloumns, calculating and filling in missing values were possible, 
    changing remaining NULL values to a more understandable value, assigning a primary key in the cleaned table when there wasn't one in original table,
    and type casting all the coloumns to the correct data type.
### Used the resulting clean tables to do analysis and answer questions.
### Did a couple quality assurance checks on the resulting clean tables to validate results and find errors

## Results
After cleaning the data tables, it was discovered that all the tables can be related to each other using columns that are the primary and 
foreign keys, this meant that the data could tell us the relationships between the colomns in different tables, for example the relationship between products
sold and country of purchse. Analyis was then done on this data set by filtering and joining the tables together to answer the questions in the project.

## Challenges 
The biggest challenged faced in this project was the data cleaning portion, there were alot of things to considered for each data table some tables took very 
large queries to generate the clean data table. Most of my bugs happened in this portion as well.

## Future Goals
One of my quality assurance checks resulted in the realization that there are still errors in the all_sessions data file, so if i had more time, i would go 
back and try to figure out why the errors exist, and weather to treat the errors as outliers and drop the rows from the data table. I woukd also have liked to come up with more questions to answer if i had more time.
