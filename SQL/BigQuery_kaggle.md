# SQL with BigQuery

[Course Homepage](https://www.kaggle.com/learn/SQL)

## Lesson 1

*From Google's description:* BigQuery](https://cloud.google.com/bigquery/docs/) is Google's fully managed, petabyte scale, low cost analytics data warehouse. BigQuery is NoOps—there is no infrastructure to manage and you don't need a database administrator—so you can focus on analyzing data to find meaningful insights, use familiar SQL, and take advantage of our pay-as-you-go model.

A nice feature of BigQuery is it's python interface. It uses a package called bq_helper to perform SQL queries and manipulations.

```python
import bq_helper
```

The focus on this lesson was just looking at a data table, the code examples are below:

```python
hacker_news = bq_helper.BigQueryHelper(active_project= "bigquery-public-data",
                                       dataset_name = "hacker_news")
hacker_news.list_tables()         # show what tables exist within the database
hacker_news.table_schema("full")  # show the schema for each column - name, data type, nullable and description
hacker_news.head("full")          # show the full data table
hacker_news.head("full", selected_columns="by", num_rows=10)
```

## Lesson 2

### SELECT and FROM

We use the keywords SELECT, FROM and WHERE to get data from specific columns based on conditions you specify. The most basic SQL query selects a single column from a single table. To do this, you specify the column you want after the word SELECT and then specify what table to pull the column from after the word FROM.

Syntax. Note that the argument we pass to FROM is not in single or double quotation marks (' or "). It is in backticks. We use it to identify the relevant BigQuery data source.

Capitalization of the commands is not required, but it is good style.

```sql
SELECT [ColumnName]
FROM [BigQueryName.DatabaseName.TableName]
```

### WHERE

Allows you to specify a condition for the retrieval.

```sql
SELECT [ColumnName]
FROM [BigQueryName.DatabaseName.TableName]
WHERE Animal == 'Cat'
```

Now, here's an example with the python helper scripts applied to the US clean air dataset.

```python
import bq_helper
open_aq = bq_helper.BigQueryHelper(active_project="bigquery-public-data",
                                   dataset_name="openaq")
open_aq.head("global_air_quality"). #this is the only table in the SQL database
```

We can look at the data table, includes a "city" column and a "country" column. So if we want to find all US cities, this would be the query string:

```python
# query to select all the items from the "city" column where the
# "country" column is "us"
query = """SELECT city
            FROM `bigquery-public-data.openaq.global_air_quality`
            WHERE country = 'US'
        """
us_cities = open_aq.query_to_pandas_safe(query)
```

This results in a Pandas dataframe. You can query multiple columns by using a comma. Can also query all columns using `*`.

What is the `query_to_pandas_safe(query, max_gb=0.1)` function do? It keeps us from pulling too much data in a single query. Another trick to prevent this is to estimate the size of the query before executing it with `BigQueryHelper.estimate_query_size()`. The estimate is given in GB.

Cool trick - if you want to only return a result once, use the `DISTINCT` command. Like this:

```python
query = """SELECT DISTINCT city
            FROM `bigquery-public-data.openaq.global_air_quality`
            WHERE country = 'US'
        """
```

## Lesson 3

### Count

COUNT(column_name) will return the number of entries in that column. So if we SELECT the COUNT() of the ID column, it will return the number of ID's in that column.

```sql
SELECT COUNT(ID)
FROM `bigquery-public-data.pet_records.pets`
```

**Aliasing** is a way to name the new column you create with a COUNT() function. So instead, would use the command `SELECT COUNT(ID) AS count`


### GROUP BY

GROUP BY takes the name of a column and groups all the rows with the same value when you apply an **aggregate function**, like COUNT() for example. Other aggregate functions include AVG(), SUM(), etc. GROUP BY is always associated with either:
1. Another GROUP BY
2. An aggregate function

### GROUP BY ... HAVING

HAVING adds a condition to the GROUP BY command. Example:

```sql
SELECT Animal, COUNT(ID)
FROM `bigquery-public-data.pet_records.pets`
GROUP BY Animal
HAVING COUNT(ID) > 1
```

A common task is to count the number of rows in a group. You can use the COUNT(1) function. You can also add a condition:

```sql
SELECT COUNT(1)
FROM `bigquery-public-data.hacker_news.comments`
WHERE deleted = True
```

## Lesson 4

Now that we can gather and group data, we want to be able to re-arrange the order of the data.

### ORDER BY

The last clause of the query.

```sql
...
ORDER BY column
```

Will order numerical columns smallest to biggest and text columns alphabetically. To reverse the order, add `DESC` at the end of the command.

### DATES


In BigQuery, dates are stores as DATE or DATETIME. The date format is the following:

`YYYY-[M]M-[D]D`

Where both the month and day are either one or two digits. DATETIME is the same, except with the time added onto the end.


To return only part of the date, use the **EXTRACT** command.

```sql
SELECT EXTRACT(DAY FROM column_with_timestamp)
FROM `bigquery-public-data.imaginary_dataset.imaginary_table`
```

SQL can handle more advanced queries from dates as well. For example, you can use `EXTRACT(WEEK ...)` to get the number of the week (from 1-53). `DAYOFWEEK` is another useful one.

Example query:

```sql
SELECT COUNT(consecutive_number) AS num_accidents, 
                  EXTRACT(DAYOFWEEK FROM timestamp_of_crash) AS day_of_week
FROM `bigquery-public-data.nhtsa_traffic_fatalities.accident_2015`
GROUP BY day_of_week
ORDER BY num_accidents DESC
```

## Lesson 5

### WITH...AS

**Common Table Expressions** (CTEs) are a way to create temporary tables within your query that are subject to additional queries. They don't exist after the query finishes running. This helps break code into chunks that are easier to read. Here's a simple example:

```sql
WITH Seniors AS 
        (
            SELECT ID, Name
            FROM `bigquery-public-data.pet_records.pets`
            WHERE Years_old > 5
        )
```

Then, under the CTE, you can perform additional queries:

```sql
SELECT ...
FROM Seniors
```

## Lesson 6

### JOIN

If you have two data tables that you want to combine data from, this is the tool to use. The key is to have a column in both tables that allows you to link the rows together. Here's there example which has two tables: one for pets and one for the owners. It's important to name each table you're using and specify throughout the query, in case some columns have the same name. ON is how you specify which columns are used for the joining.

```sql
SELECT p.Name AS Pet_Name, o.Name as Owner_Name
FROM `bigquery-public-data.pet_records.pets` as p
INNER JOIN `bigquery-public-data.pet_records.owners` as o 
    ON p.ID = o.Pet_ID
```

### INNER JOIN

A row will only be put in the final output table if the value in the column you're using to combine them shows up in both the tables you're joining. For example, if Tom's ID code of 4 didn't exist in the "pets" table, we would only get 3 rows back from this query. There are other types of JOIN, but an INNER JOIN is very widely used.

## Additional Notes

I also checked out [Mode](https://mode.com/sql-tutorial/)'s SQL tutorials to find some additional commands that look like they'll be useful.

### LIKE

LIKE is a way to make more general queries for text data. ILIKE is a related command that ignores text case when querying data. `%` is used as the wild-card character for the LIKE commands. `_` is used to indicate a wild-card for a single character

```sql
SELECT *
  FROM tutorial.billboard_top_100_year_end
 WHERE "group" ILIKE 'snoop%'
 ```

### IN, BETWEEN

These are both ways to do more complex logical operations within a WHERE statement. IN lets you retrieve matches from a vector of possibilities, and this works with numbers and with text. BETWEEN...AND lets you retrieve matches that are between two values.

```sql
SELECT *
  FROM tutorial.billboard_top_100_year_end
 WHERE artist IN ('Taylor Swift', 'Usher', 'Ludacris')
```

```sql
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year_rank BETWEEN 5 AND 10
```

A note for BETWEEN - it includes the edge cases (5 and 10 are included, in this example)

### CASE, WHEN, THEN, ELSE

This is how if/then logic is implemented. CASE is always followed by at least one pair of WHEN and THEN statements (the equivalent of IF/THEN). Every CASE must end with and END. Here's an example from a football roster data table:

```sql
SELECT player_name,
       year,
       CASE WHEN year = 'SR' THEN 'yes'
            ELSE NULL END AS is_a_senior
  FROM benn.college_football_players
```

This query looks at the 'year' column. If the player is a 'SR', then it creates a column with the 'yes' value. Otherwise, it leaves that column as NULL.

