## Versioning in Databricks
Databricks supports restoring tables to a specific version.

### Commands:
- **View all table versions**:  
  ```sql
  DESCRIBE HISTORY catalog_name.schema_name.table_name;
  ```
- **Select a specific version**:  
  ```sql
  SELECT * FROM catalog_name.schema_name.table_name VERSION AS OF version_number;
  SELECT * FROM catalog_name.schema_name.table_name TIMESTAMP AS OF 'timestamp';
  ```
- **Restore a table**:  
  ```sql
  RESTORE TABLE catalog_name.schema_name.table_name TO VERSION AS OF version_number;
  RESTORE TABLE catalog_name.schema_name.table_name TO TIMESTAMP AS OF 'timestamp';
  ```

### PySpark Equivalents:
- **Select a specific version**:  
  ```python
  spark.read.format("delta").option("versionAsOf", 1).table("catalog_name.schema_name.table_name")
  spark.read.format("delta").option("timestampAsOf", "timestamp").table("catalog_name.schema_name.table_name")
  ```
- **Restore a table**:  
  ```python
  from delta.tables import DeltaTable
  table_dt = DeltaTable.forName(spark, "catalog_name.schema_name.table_name")
  table_dt.restoreToVersion(1)
  ```

---
