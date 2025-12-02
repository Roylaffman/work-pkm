# **How PKs and FKs Are Used in Data Pipelines**

In data engineering and ETL processes (like DBT, Dagster, FME, Power BI):

### PKs are used to:

- Identify unique records
    
- Detect changes (CDC – Change Data Capture)
    
- Merge new data into existing tables
    
- Deduplicate data
    

### FKs are used to:

- Rebuild relationships after data moves
    
- Join tables correctly
    
- Ensure referential integrity in the pipeline
    
- Validate that the destination data matches the source schema
    

### Example Data Pipeline Flow:

1. Pull pipeline data from upstream database
    
2. Use **SegmentID (PK)** to identify existing records
    
3. Use **FKs** to pull related attributes, inspection data, or flow meter data
    
4. Merge into Select Water enterprise schemas
    
5. Validate relationships using PK→FK mapping
    
6. Load into Portal, Power BI, or KMZ exporters
    

Without PKs and FKs:

- Pipelines break
    
- Joins produce duplicates
    
- Mismatched records occur
    
- Automated data syncing becomes unreliable
    

---

# 🎯 Why PKs and FKs Matter SO MUCH in Select Water GIS + Data Pipelines

Because you integrate:

- Enterprise geodatabases
    
- FME Flow
    
- ArcGIS Portal
    
- KMZ exporters
    
- Power BI
    
- DBT + Dagster
    

…these systems rely heavily on PKs (usually GlobalIDs) and FKs to:

### ✔ Keep assets consistent across maps and databases

### ✔ Track updates to pipelines & facilities

### ✔ Relate inspection tables to geometry

### ✔ Sync data between Enterprise, Portal, and KMZ exports

### ✔ Maintain correct joins in dashboards (Power BI)

### ✔ Prevent geometry/attribute mismatches

### ✔ Ensure traceability of updates

---

# 📘 Quick Summary (Copy/Paste Ready)

**Primary Key (PK):**  
A unique identifier for each record in a table or feature class.  
Examples: `GlobalID`, `SegmentID`, `API`.

**Foreign Key (FK):**  
A field that references a PK in another table to establish a relationship.  
Examples: `Pipeline_GlobalID`, `FacilityID`, `CaseTypeCode`.

**In GIS:**

- PK = GlobalID
    
- FK = GUID fields referencing other feature classes or tables.
    

**In data pipelines:**

- PKs track unique records and identify changes
    
- FKs maintain relationships after data transformations
    
- PK/FK ensure accurate joins and clean data flows