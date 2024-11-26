
### 1. **When creating a managed table, where is it saved?**

In Databricks, when you create a **managed table**, the data and metadata are stored entirely within the metastore's storage. For the Unity Catalog metastore, the default storage location is a **managed storage location**, often specified during the setup of the Unity Catalog metastore. By default, this storage location resides in a cloud-based object store (e.g., Amazon S3, Azure Data Lake Storage, or Google Cloud Storage).

---

### 2. **Why do we need Storage Credentials and External Locations?** [YouTube Video](https://www.youtube.com/watch?v=kRfNXFh9T3U)

**Storage Credentials** and **External Locations** are primarily used to access files in cloud object storage. But let’s go deeper:
#### **Storage Credentials**:
These define how Databricks can authenticate and access a cloud storage service. They act as a bridge between Databricks and your cloud environment.

#### **External Locations**:
An **external location** is essentially a pointer to a specific path in your cloud storage that uses the defined storage credentials. By creating an external location, you:
- Securely manage access to data in the cloud.
- Use it for external (non-managed) tables or direct access without importing data into the metastore's managed storage.

#### Why is it called "external"?
- It’s external because the data is not fully controlled or managed by the Unity Catalog or the metastore.
- The data resides in your cloud object storage and **was not created or governed by the catalog's default managed storage mechanism**.

---

### Summary:
- **Managed Table**: Data stored in the Unity Catalog’s **metastore directory**.
- **Storage Credentials**: Provide authentication to access cloud storage.
- **External Location**: A defined path in external storage accessed via credentials.
- **Internal Location**: Default storage for Unity Catalog-managed data (metastore directory).
