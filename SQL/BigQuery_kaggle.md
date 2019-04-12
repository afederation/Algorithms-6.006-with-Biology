# SQL with BigQuery

## Lesson 1

[Link](https://www.kaggle.com/dansbecker/getting-started-with-sql-and-bigquery)

[BigQuery](https://cloud.google.com/bigquery/docs/) is Google's fully managed, petabyte scale, low cost analytics data warehouse. BigQuery is NoOps—there is no infrastructure to manage and you don't need a database administrator—so you can focus on analyzing data to find meaningful insights, use familiar SQL, and take advantage of our pay-as-you-go model.

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

[Link](https://www.kaggle.com/dansbecker/select-from-where)

**SELECT and FROM**

We use the keywords SELECT, FROM and WHERE to get data from specific columns based on conditions you specify. The most basic SQL query selects a single column from a single table. To do this, you specify the column you want after the word SELECT and then specify what table to pull the column from after the word FROM.

Syntax. Note that the argument we pass to FROM is not in single or double quotation marks (' or "). It is in backticks. We use it to identify the relevant BigQuery data source.

Capitalization of the commands is not required, but it is good style.

```sql
SELECT [ColumnName]
FROM [BigQueryName.DatabaseName.TableName]
```

**Where**

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







