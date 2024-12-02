## Why Use the Delta Table API?
The Spark DataFrame API cannot perform certain operations on Delta Tables directly, such as:
- Deleting data
- Updating data
- Merging data

Using the DataFrame API for such operations requires:
1. Reading the entire table
2. Filtering/transforming using `.filter` or `.where`
3. Writing a new table with `.write().mode("overwrite")`

This is inefficient for operations like deleting a single row. Instead, use **SparkSQL** or the **Delta Table API**.

In the notebook associated you will find the use of Delta Table API.
---

