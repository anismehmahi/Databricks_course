
# Schema Validation in Databricks

When adding new rows to a table, Databricks validates the schema of the table against the schema of the incoming data. Here are the 4 insertion scenarios:

1. **INSERT**:  
   - Column matching by **position**  
   - **New columns are not allowed**  

2. **OVERWRITE**:  
   - Column matching by **position**  
   - **New columns are not allowed**  

3. **MERGE INSERT**:  
   - Column matching by **name**  
   - **New columns are ignored**  

4. **DATAFRAME APPEND**:  
   - Column matching by **name**  
   - **New columns are not allowed**  

5. **Data Type Mismatch**:  
   - **Not allowed** in any scenario  

---

## Observations
- **DataFrame Append** is the safest option to add new records to a table.  
- None of the above options allows adding new columns when adding new rows.

---

## Solutions
1. **Manual Schema Update**:  
   Add the new column manually to the table that will receive the new data, then insert the data.

2. **Automatic Schema Evolution**:  
   Enable schema evolution when appending data:  
   ```python
   df.write.format("delta").mode("append").option("mergeSchema", "true").saveAsTable("table_name_to_add_new_data_into")
   ```
```
