
### **Databricks Unity Catalog: Overview**

The **Unity Catalog** in Databricks is an important feature for managing and organizing data  across multiple workspaces. Let’s break it down step by step:

---

#### **Why is Unity Catalog Important?**
- In Databricks, each workspace has its own **databases (schemas)**. For example:
  - Team A working in Workspace 1 cannot access the databases in Workspace 2 where Team B operates, which is a problem.
- **Unity Catalog** comes into play to solve this probkem by enabling:
  - A centralized data management component that shares data across multiple workspaces.
---

#### **How Does Unity Catalog Work?**

1. **Metastore Setup**:
   - Start by creating a **metastore** and add to it the workspaces 1 and 2 (The primary purpose is to enable data sharing across these workspaces).

2. **Catalog Organization**:
   - Within the metastore, you create **catalogs** to organize data further.
   - Each catalog contains inside **Databases (schemas)** and in each database we have tables, views, volumes, functions and ml models (these are called data objects).

3. **Referencing Data**:
   - To access data, use the following structure:  
     `spark.read.table("catalog_name.database_name.table_name")`
   - Example: This command accesses a table in a specific database within a catalog.

4. **Permissions and Governance** (See more below):
   - Permissions can be set at different levels:
     - Catalog level.
     - Database (schema) level.
     - Table level.
       
5. **External Data Access**:
   - Unity Catalog allows us to integrate external data sources like Snowflake, MySQL, or PostgreSQL, by establishing connections to those systems.

---

### **Access Policies and Permissions in Unity Catalog**

#### **Permissions Overview**
Unity Catalog supports a hierarchy of permissions for catalogs, databases, and tables. Below is the structure:

```
                     Catalog_1
      (USE CATALOG / USE SCHEMA / SELECT ...)
          ______________________________
         |               |              |
     Database_1      Database_2      Database_3
    (USE SCHEMA)    (USE SCHEMA)    (USE SCHEMA)
     /   |   \        /   |   \        /   |   \
    T1   T2   T3     T4   T5   T6     T7   T8   T9
```

#### ** Some Permission Types** [YouTube Video](https://www.youtube.com/watch?v=9HxSWYUeQgE&list=PL6pAXQQpiH-mi8AO5_UDPpOPPjted9kCv&index=9)

1. **USE CATALOG**:
   - Found **only** at the catalog level.
   - Allows the user to:
     - **Only** See the name of that catalog.
     - and **Not** the schemas or tables names inside that catalog.

2. **USE SCHEMA**:
   - Found at both the catalog and schema levels:
     - **Catalog Level**: Allows the user to see **only** the names of all schemas within the catalog (but not the tables inside those schemas).
     - **Schema Level**: Allows the user to see **only** the name of a specific schema (but not the tables inside it).

3. **SELECT**:
   - Found at the catalog and schema levels:
     - **Catalog Level**: Allows the user to select data from any table in that catalog (requires the user to have `USE CATALOG` and `USE SCHEMA` permissions).
     - **Schema Level**: Allows the user to select data from any table in that schema (requires `USE SCHEMA`).

#### **Explicit vs. Inherited Permissions**
When we say that `USE CATALOG` and `USE SCHEMA` are explicit and not inherited, we mean that giving a user `USE CATALOG` does not give him the power to use schemas, so he needs `USE SCHEMA` also.
Not like if we give him `SELECT` at the catalog level he can automatically select all tables inside any schema (if he has `USE SCHEMA` and `USE CATALOG` of course).
