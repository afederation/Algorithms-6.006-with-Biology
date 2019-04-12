# SQL with BigQuery

## Lesson 1

[Link](https://www.kaggle.com/dansbecker/getting-started-with-sql-and-bigquery)

[BigQuery](https://cloud.google.com/bigquery/docs/) is Google's fully managed, petabyte scale, low cost analytics data warehouse. BigQuery is NoOps—there is no infrastructure to manage and you don't need a database administrator—so you can focus on analyzing data to find meaningful insights, use familiar SQL, and take advantage of our pay-as-you-go model.

A nice feature of BigQuery is it's python interface. It uses a package called bq_helper to perform SQL queries and manipulations.

```python
import bq_helper
```

The focus on this lesson was just looking at a datatable, the code examples are below:

```python
hacker_news = bq_helper.BigQueryHelper(active_project= "bigquery-public-data", 
                                       dataset_name = "hacker_news")
hacker_news.list_tables()         # show what tables exist within the database
hacker_news.table_schema("full")  # show the schema for each column - name, data type, nullable and description
hacker_news.head("full")          # show the full data table
hacker_news.head("full", selected_columns="by", num_rows=10)
```

## Lesson 2
