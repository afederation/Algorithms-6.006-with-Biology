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