# 📁 **What is a PBIX File?**

A **PBIX (.pbix)** file is a **Power BI Desktop project file** that contains:

### ✔ Data model

- Imported or connected datasets
    
- Tables, relationships, data types, measures, calculated columns
    
- Query transformations from Power Query (M code)
    

### ✔ Report visuals

- Charts, maps, tables, KPIs
    
- Layout, formatting, interactions, filters
    

### ✔ Metadata

- Measures (DAX)
    
- Report pages
    
- Themes
    
- RLS (Row-Level Security) settings
    
- Model relationships (star/snowflake schema)
    

A PBIX is **everything you build in Power BI Desktop**, packaged into a single file.

---

# 🧩 **What’s inside a PBIX?**

Think of it like a zip bundle containing:

- **Data model** (semantic model, compressed using VertiPaq engine)
    
- **Report definition** (JSON & metadata)
    
- **Queries** (Power Query M scripts)
    
- **Data** (imported tables stored in-memory)
    
- **Custom visuals and themes**
    

---

# ⚡ Why is the PBIX important?

It is:

### **The editable source file**

You create and modify reports here.

### **What you publish to Power BI Service**

When you click **Publish**, Power BI uploads the PBIX contents to the cloud.

### **How you share reports with developers**

PBIX files are used in version control, CI/CD pipelines, and team collaboration.

---

# 🆚 PBIX vs PBIT vs PBIXL

|Format|Meaning|Purpose|
|---|---|---|
|**PBIX**|Full report + data|Standard editable file|
|**PBIT**|Power BI Template|Report + queries, **no data**|
|**PBIXL**|Preview feature|Power BI Project JSON source structure for Git integration|

---

# 🔍 How PBIX files are typically used

### In your workflow (Select Water / SWS DEA context):

- Visualizing geospatial assets
    
- Dashboarding disposal & recycle facility metrics
    
- Visualizing Power BI dashboards fed by Dagster/DBT pipelines
    
- Creating executive dashboards for OPS, BD, and ESG
    
- Publishing for management and field operations to use
    

---

# 🛑 Important Notes

- PBIX files **can get large** if data is imported (in-memory).
    
- They can be **binary**, not easily diffable.
    
- New Git-friendly PBIP format is beginning to replace PBIX for source control—but PBIX is still the main format today.
    

---

# 📚 Summary

A **PBIX file = Power BI Desktop report**, containing:

- Data model
    
- Data transformations
    
- Visualizations
    
- Measures (DAX)
    
- Metadata
    

It is the core, editable file format for working with Power BI.

---

If you'd like, I can also explain:

✔ How PBIX fits into the Power BI architecture  
✔ How PBIX works with DBT & Dagster  
✔ The difference between Import vs DirectQuery models  
✔ Why PBIX is hard to version control and the new PBIP alternative