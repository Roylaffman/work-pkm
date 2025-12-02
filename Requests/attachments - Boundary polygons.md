---
tags:
  - work
  - gis
  - pit
---





Excellent — this is an important (and sometimes tricky) step in enterprise data migrations.  
Since you’re using **ArcGIS Pro with an Enterprise Geodatabase in PostgreSQL**, here’s the **best-practice workflow** to capture and migrate **all attachments** from your _old feature class_ to your _new one_ before retiring the old dataset.

---

## 🧭 Overview

In ArcGIS Enterprise (Postgres or SQL Server), **attachments** are not stored inside the feature class table.  
They are stored in a **related attachment table**, which Esri automatically creates when you enable attachments.

For example, if your feature class is:

`facilities`

ArcGIS will create:

`facilities__ATTACH facilities__ATTACHREL`

So your goal is to:

1. Export the old attachments and their relationship IDs.
    
2. Match or map those relationships to your new feature class’s object IDs.
    
3. Reattach them to the new feature class.
    

---

## 🧩 Step-by-Step: Safest Workflow

### **1️⃣ Identify attachment tables**

In **pgAdmin** or **ArcGIS Catalog pane**, look for:

`old_featureclass__ATTACH old_featureclass__ATTACHREL`

Each contains:

- `ATTACHMENTID`
    
- `REL_GLOBALID` (link to parent feature’s `GlobalID`)
    
- `CONTENT_TYPE`, `DATA_SIZE`, `ATT_NAME`, and `DATA` (the binary BLOB of the file)
    

---

### **2️⃣ Ensure your new feature class has GlobalIDs and attachments enabled**

In ArcGIS Pro:

1. Right-click your new feature class → **Manage → Add Global IDs**
    
2. Right-click again → **Manage → Enable Attachments**
    

This will create:

`new_featureclass__ATTACH new_featureclass__ATTACHREL`

---

### **3️⃣ Export attachments from the old feature class**

**Option A: Use the Geoprocessing Tool**

- Go to **Data Management → Attachments → Export Attachments**
    
- Input: `old_featureclass`
    
- Output Folder: `C:\Temp\Attachments_Export`
    
- Output Table: (optional) include an attribute table that logs which attachment belongs to which feature’s GlobalID.
    

This extracts all attachments as actual files (.jpg, .pdf, etc.) and an accompanying CSV linking them to their parent features.

**Option B: Do it via SQL (if scripting)**

`SELECT rel."REL_GLOBALID",        att."ATT_NAME",        att."CONTENT_TYPE",        att."DATA" FROM   old_featureclass__ATTACH att JOIN   old_featureclass__ATTACHREL rel ON     att."REL_GLOBALID" = rel."REL_GLOBALID";`

This lets you export attachments programmatically (e.g., using Python or FME).

---

### **4️⃣ Create a mapping between old and new GlobalIDs**

You need a **crosswalk** table or CSV that matches features from the old FC to the new FC.  
Typical join fields:

- `old_featureclass.OLD_ID` ↔ `new_featureclass.NEW_ID`
    
- Then derive `old_featureclass.GLOBALID` ↔ `new_featureclass.GLOBALID`.
    

You can do this in ArcGIS Pro:

- Use **Add Join** between old and new feature classes using a stable field like `SiteName`, `FacilityID`, etc.
    
- Export the joined table with both GlobalIDs.
    

---

### **5️⃣ Reattach using “Add Attachments” tool**

Once you have:

- The exported attachments
    
- A table linking old GlobalID → new GlobalID
    

Run:

> **Data Management → Attachments → Add Attachments**

Input:

- **Input Dataset:** your **new feature class**
    
- **Input Join Field:** `GLOBALID` (new)
    
- **Match Table:** your mapping table (the crosswalk)
    
- **Match Join Field:** `OLD_GLOBALID`
    
- **Match Attachments Folder:** where your exported attachments are stored
    
- **Match File Field:** the column in your mapping table with attachment file names.
    

ArcGIS will automatically create records in the `new_featureclass__ATTACH` table and attach them to the correct features.

---

### **6️⃣ QA/QC and Verification**

After running **Add Attachments**, verify:

- The **Attachments** tab in the feature’s pop-up in ArcGIS Pro shows the correct number and filenames.
    
- The record count of:
    
    `old_featureclass__ATTACH new_featureclass__ATTACH`
    
    should match (within expected mapping tolerances).
    

Optional SQL check:

`SELECT COUNT(*) FROM old_featureclass__ATTACH; SELECT COUNT(*) FROM new_featureclass__ATTACH;`

---

## ⚙️ Optional: Automate with Python

You can automate the whole process with **ArcPy** if you need to repeat it for multiple layers:

`import arcpy  old_fc = r"Database Connections\EGIS.sde\egis.schema.old_featureclass" new_fc = r"Database Connections\EGIS.sde\egis.schema.new_featureclass" export_folder = r"C:\Temp\Attachments"  # 1. Export old attachments arcpy.ExportAttachments_management(old_fc, export_folder)  # 2. Join GlobalIDs manually or via mapping table  # 3. Add attachments to new feature class mapping_table = r"C:\Temp\MappingTable.csv" arcpy.AddAttachments_management(new_fc, "GLOBALID", mapping_table,                                 "NEW_GLOBALID", "FILE_NAME", export_folder)`

---

## 🧱 Best Practice Notes

- Always **backup** both attachment tables before modifying or retiring a feature class.
    
- If using **versioned data**, ensure you reconcile/post before attachment transfer.
    
- If possible, **freeze edits** on the old FC until migration is verified.
    
- For large datasets, consider using **FME** to automate this process — it can read attachment BLOBs directly from PostgreSQL and reinsert them into the new tables.
    

---

## ✅ Summary

|Step|Tool / Action|Goal|
|---|---|---|
|1|Identify `_ATTACH` and `_ATTACHREL` tables|Locate old attachments|
|2|Enable attachments on new FC|Prepare destination|
|3|Export attachments|Extract files + relationships|
|4|Create GlobalID mapping|Crosswalk old → new|
|5|Add attachments|Load files to new FC|
|6|QA/QC|Verify counts and links|