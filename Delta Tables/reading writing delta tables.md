
# Delta Tables in Databricks
## 1. Write Managed Table
- **Spark SQL**:  
  ```sql
  CREATE TABLE IF NOT EXISTS catalog_name.db_name.managed_table_name (
    column1 DATE, 
    column2 STRING, 
    column3 INT
  ) 
  USING DELTA;
  ```
- **PySpark**:  
  ```python
  df.write.format("delta").saveAsTable("catalog_name.db_name.managed_table_name")
  ```

## 2. Read Managed Table
- **Spark SQL**:  
  ```sql
  SELECT * FROM catalog_name.db_name.managed_table_name;
  ```
- **PySpark**:  
  ```python
  df = spark.read.format("delta").table("catalog_name.db_name.managed_table_name")
  ```

## 3. Write Table into External Location
- **PySpark**:  
  ```python
  df.coalesce(1).write.format("delta").save("abfss://container_name@account_storage.dfs.core.windows.net/")
  ```

## 4. Read Table from External Location
- **Spark SQL**:  
  ```sql
  SELECT * FROM catalog_name.db_name.external_table_name;
  ```
  *(Note: The external table must first be added to the catalog. See 5-a below.)*

- **PySpark**:  
  ```python
  df = spark.read.format("delta").schema(schema).load("abfss://container_name@account_storage.dfs.core.windows.net/")
  ```

## 5. Register a Delta Table into Catalog from an External Location
- **Spark SQL**:  
  ```sql
  CREATE TABLE IF NOT EXISTS catalog_name.db_name.external_table_name (
    column1 DATE, 
    column2 STRING, 
    column3 INT
  ) 
  USING DELTA 
  LOCATION 'abfss://container_name@account_storage.dfs.core.windows.net/';
  ```

## 6. Save Managed Table from a File
**Note**: Saving data this way results in it being saved twice (in external location and in the catalog).  

### Steps:
1. **Load the table from a file with its schema**:  
   ```python
   df = spark.read.format("delta").schema(schema).load("path_to_file")
   ```
   Then, write as a managed table:  
   ```python
   df.write.format("delta").saveAsTable("catalog_name.db_name.managed_table_name")
   ```

2. **Alternatively**:  
   ```sql
   CREATE TABLE catalog_name.db_name.managed_table_name (
     name STRING, 
     age INT, 
     city STRING
   ) 
   USING JSON 
   OPTIONS (path '/mnt/data/sample.json');
   ```


## 7. Converting Parquet Files to a Delta Table
If you have a dataset as multiple Parquet files in an external location, you can convert it to a Delta Table :

```sql
CONVERT TO DELTA parquet.`path_to_directory_in_which_parquet_files_are_in` PARTITIONED BY (column_name column_type);
```

You can now register those files as a Delta Table into Catalog (see step 5) and start querying this data.

---
## Notes
- **External Tables**:  
  Dropping an external table (e.g., `DROP TABLE catalog_name.db_name.external_table_name;`) does not delete the storage or data; it only removes the table from the catalog.
- Ensure proper **storage credentials** and **external location setup** before working with external tables.

---

