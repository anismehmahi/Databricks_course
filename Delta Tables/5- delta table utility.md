# VACUUM, REORG, OPTIMIZE, and ZORDER in Databricks

## VACUUM
We’ve discussed versioning in Delta tables. Consider a scenario where you delete two columns from a table. Thanks to versioning, the table before deletion is saved. However, what if you want to permanently delete that version?

### Usage:
- **VACUUM Command**:  
  ```sql
  VACUUM catalog_name.schema_name.table_name RETAIN 165 HOURS DRY RUN;
  ```
  - **165 HOURS**: Deletes versions older than one week. To keep only the latest version, use `0 HOURS`.
  - **DRY RUN**: Shows the files that will be deleted without actually deleting them.  
    Run the command first with `DRY RUN` to review the files, then execute without it to perform the actual deletion.

### Important Notes:
- If you specify a retention period of less than 165 hours (e.g., `x HOURS < 165`), you might encounter a safety error because Databricks enforces a minimum of one week of history.
- To override this restriction, disable the safety check:
  ```python
  spark.databricks.delta.retentionDurationCheck = false
  ```

---

## REORG and VACUUM
### Scenario:
You’re asked to delete two columns from a table. If you try:
```sql
ALTER TABLE table_name DROP columns(column_1, column_2);
```
You’ll encounter a safety error. 

### Steps to Resolve:
1. Enable **Column Mapping Mode**:
   ```sql
   ALTER TABLE table_name SET TBLPROPERTIES ('delta.columnMapping.mode'='name');
   ```
   - This will not physically delete the columns but will mask them. When you run:
     ```sql
     SELECT * FROM table_name;
     ```
     the columns will be hidden, but they still exist physically (you can verify this in Azure ALSD).  

2. To **physically delete the columns**, use the **REORG** command:
   ```sql
   REORG TABLE table_name APPLY (PURGE);
   ```
   - This creates a new table (new files in Azure ALSD) without the two columns.  

3. **Clean up old files**:
   - Use `VACUUM` to remove files from the previous version of the table (where the two columns still exist).  
   - Always follow REORG with a VACUUM command.

---

## OPTIMIZE and ZORDER
### Purpose:
Reorganize the files of a table into evenly balanced data files and colocate them based on specified columns for better query performance.

### Command:
```sql
OPTIMIZE table_name ZORDER BY (column_1, column_2);
```

### Important Notes:
- Similar to REORG, the OPTIMIZE and ZORDER commands create reorganized files but retain the files from the previous table.  
- **Follow-up with VACUUM**:
  - To clean up files from the previous versions and keep only the optimized version, always run a VACUUM after performing OPTIMIZE or ZORDER.
```
