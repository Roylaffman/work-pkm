---
tags:
  - gis
  - fc
---
# No Schema Report



# Decimals percison and Scale
![[Pasted image 20251029161026.png]]

## 🧭 1. Understanding Precision and Scale (GIS and Data Science Contexts)

### **In GIS (Esri Geodatabases)**

When you define numeric fields (like `DOUBLE` or `FLOAT`), **precision** and **scale** determine how many digits are stored.

- **Precision (P)** = total number of digits stored (before + after the decimal). **the 38**
    
- **Scale (S)** = number of digits to the right of the decimal. **the 8**
    

So for a **`DOUBLE(38,8)`**, you can store:

- Up to **38 total digits**,
    
- With **8 digits after the decimal**.  
    This easily supports global coordinates like:
    

`Latitude: 32.54889412 Longitude: -104.32261144`

If instead you had **`(7,1)`**, you could only store one digit after the decimal — your coordinate would round to something like `32.5`, which is far too coarse (roughly ±11 km of error).

🧩 _In ArcGIS_:

- `DOUBLE` fields **ignore precision and scale** when created via arcpy in file/enterprise GDBs — they default to database settings (often `38,8` in SQL-based systems like PostgreSQL).
    
- But when fields are created through **ArcGIS Pro GUI or DDL**, you can set precision/scale explicitly.
    

---

### **In Data Science / General Computing**

In data science:

- `float64` (the equivalent of `DOUBLE`) stores ~15–16 significant digits of precision — enough for centimeter-level accuracy in lat/long.
    
- Precision loss occurs if your data type is `float32` (only ~7 significant digits).
    

Thus, for geographic work, you always want **`DOUBLE(38,8)`** (or equivalent 64-bit float).

---

## 🧱 2. How to Ensure Your Coordinates Are Accurate (`DOUBLE(38,8)`)

In an **Enterprise Geodatabase** (PostgreSQL, SQL Server, Oracle), you can specify the precision and scale explicitly in arcpy by adding parameters:

`arcpy.management.AddField(     in_table=fc_path,     field_name="gis_lat",     field_type="DOUBLE",     field_precision=38,     field_scale=8,     field_alias="GIS Latitude" )`

Do this for both your latitude and longitude fields.  
For other numeric fields (like distances or angles), you can set smaller scales (e.g., `10,2`).

---

## 🧩 3. Creating the New Feature Class: **Select Pigging Stations**

Here’s how to make sure your new **point feature class** is properly built with Z-values, correct projection, and accurate numeric fields:

### ✅ **Steps:**

`import arcpy  # --- USER CONFIG --- sde_path = r"D:\Users\rlafferty\AppData\Roaming\Esri\ArcGISPro\Favorites\PostgreSQL-gisdb-select_gis_dev(rlafferty).sde" fc_name = "select_pigging_stations" fc_alias = "Select Pigging Stations"  # Spatial Reference: NAD83 / UTM Zone 13N (replace with your region’s projection) SR = arcpy.SpatialReference(26913)  # --- CREATE FEATURE CLASS --- fc_path = f"{sde_path}\\{fc_name}" if arcpy.Exists(fc_path):     arcpy.management.Delete(fc_path)  arcpy.management.CreateFeatureclass(     out_path=sde_path,     out_name=fc_name,     geometry_type="POINT",     has_z="ENABLED",             # <-- Enable Z-values     spatial_reference=SR )  # --- ADD FIELDS --- fields = [     ("station_id", "Station ID", "TEXT", 50),     ("station_name", "Station Name", "TEXT", 100),     ("status", "Status", "TEXT", 50),     ("install_date", "Install Date", "DATE", None),     ("gis_lat", "Latitude", "DOUBLE", None),     ("gis_long", "Longitude", "DOUBLE", None),     ("elevation", "Elevation (ft)", "DOUBLE", None) ]  for name, alias, ftype, length in fields:     if not arcpy.ListFields(fc_path, name):         arcpy.management.AddField(             in_table=fc_path,             field_name=name,             field_type=ftype,             field_alias=alias,             field_length=length if (ftype == "TEXT" and length) else None,             field_precision=38 if ftype == "DOUBLE" else None,             field_scale=8 if ftype == "DOUBLE" else None         )  print(f"✅ Feature class created successfully: {fc_path}")`

---

### 🧭 **Checklist for Success**

|Requirement|How to Ensure It|
|---|---|
|**Z-Values Enabled**|Set `has_z="ENABLED"` in `CreateFeatureclass`.|
|**Correct Projection**|Use an appropriate **projected coordinate system** (e.g., NAD83 UTM Zone 13N for Permian).|
|**Lat/Long Precision**|Use `DOUBLE(38,8)` for latitude/longitude.|
|**Clean Field Names**|Avoid typos like `pit_cameria_id` — use consistent naming.|
|**No Overwrite Issues**|Check `OVERWRITE_IF_EXISTS = True` only when needed.|
|**Validation**|After creation, right-click the layer in ArcGIS Pro → _Properties → Fields_ and confirm precision/scale.|







##  Option 1: If Your Excel File Has Coordinates (XY)

If your .xlsx file has columns like `Latitude` and `Longitude`, you can do the following:

1. **Add Excel to ArcGIS Pro**:
    
    - In the Catalog pane, right-click "Folders" > Add Folder Connection > Browse to your Excel file's folder.
        
    - Drag the Excel sheet into the Contents pane.
        
2. **Create Feature Class**:
    
    - Go to **Analysis** > **Tools**.
        
    - Search for **"XY Table To Point"**.
        
    - Input table = your Excel sheet.
        
    - Set X Field (e.g., `Longitude`) and Y Field (e.g., `Latitude`).
        
    - Choose a coordinate system (e.g., WGS 1984).
        
    - Run it. It creates a new feature class from the Excel sheet.
        

---

## Option 2: If You Just Have Schema Info in Excel 

If your Excel file just defines the schema (like a list of field names, types, and lengths), follow these steps:

### 🔧 Step 1: Prepare the Schema in a Readable Format

Ensure your Excel sheet has at least:

|Field Name|Data Type|Length|
|---|---|---|
|Name|Text|50|
|ID|Integer||
|DateAdded|Date||

You can add other fields as needed.

---

### 🔧 Step 2: Create an Empty Feature Class

1. **Open ArcGIS Pro** > Go to **Catalog Pane**.
    
2. Right-click a **geodatabase** > **New** > **Feature Class**.
    
3. Name it, choose the geometry type (Point, Polyline, Polygon).
    
4. On the "Fields" page, **manually enter fields using your Excel schema**:
    
    - Use your Excel file as a reference to type each field name, type, and length.
        
5. Finish the wizard to create the feature class.
    

---

### 🛠 Optional: Automate Schema Import (Advanced – with Python)

If your Excel schema is large, you can automate it with a Python script using ArcPy:

`import arcpy import pandas as pd  # Load Excel schema df = pd.read_excel(r"C:\path\to\schema.xlsx")  # Set workspace and feature class name arcpy.env.workspace = r"C:\Path\To\Your\Geodatabase.gdb" feature_class_name = "MyFeatureClass"  # Create empty feature class arcpy.CreateFeatureclass_management(arcpy.env.workspace, feature_class_name, "POINT")  # Add fields based on schema for index, row in df.iterrows():     field_name = row['Field Name']     field_type = row['Data Type']     field_length = row.get('Length', None)          if field_type.upper() == "TEXT" and pd.notna(field_length):         arcpy.AddField_management(feature_class_name, field_name, field_type.upper(), field_length=int(field_length))     else:         arcpy.AddField_management(feature_class_name, field_name, field_type.upper())`

> 📝 Make sure your Excel file columns match the script (Field Name, Data Type, Length


# Schema Report 

## 🧾 What Is a Schema Report?

A **schema report** is typically an Excel or XML file that describes the structure of a geodatabase object — including field names, data types, lengths, domains, and more.

However, **ArcGIS Pro cannot directly import a schema report to create a feature class** — it's meant more for documentation. But we can still use the schema report to **manually recreate** or **automate** the creation of the feature class.

---

## ✅ Your Two Main Options

### 🔹 OPTION 1: Manual Method (Quick but manual)

Use the schema report as a reference and **manually recreate the fields** in ArcGIS Pro.

#### Steps:

1. In ArcGIS Pro:
    
    - Open a project and go to the **Catalog pane**.
        
    - Right-click your **target geodatabase** > **New** > **Feature Class**.
        
2. Step through the wizard:
    
    - Set name, geometry type, coordinate system.
        
    - In the **Fields** step, refer to your schema report and enter each field:
        
        - Name
            
        - Data type (Text, Integer, Date, etc.)
            
        - Length (for Text)
            
        - Any domain, if applicable (you’ll need to predefine domains if used).
            
3. Finish and create the feature class.
    

> Good for: small or moderate number of fields.

---

### 🔹 OPTION 2: Automated Method (Using Python/ArcPy)

If your schema report is large, **automate** the feature class creation using ArcPy + pandas (Python).

This assumes your schema report is in **Excel format** with columns like:

- Field Name
    
- Data Type
    
- Length
    
- Nullable
    
- Domain (optional)
    

#### 🧠 Sample Python Script

`import arcpy import pandas as pd  # Path to Excel schema report excel_path = r"C:\Path\To\Your\SchemaReport.xlsx" sheet_name = "Fields"  # adjust if needed  # Read Excel df = pd.read_excel(excel_path, sheet_name=sheet_name)  # Set workspace arcpy.env.workspace = r"C:\Path\To\Your\Geodatabase.gdb" feature_class_name = "NewFeatureClass"  # Create empty feature class arcpy.CreateFeatureclass_management(out_path=arcpy.env.workspace,                                     out_name=feature_class_name,                                     geometry_type="POINT")  # Change as needed  # Add fields for index, row in df.iterrows():     field_name = row['Field Name']     field_type = row['Data Type'].upper()     length = row.get('Length', None)      # Handle Text length     if field_type == 'TEXT' and pd.notna(length):         arcpy.AddField_management(feature_class_name, field_name, field_type, field_length=int(length))     else:         arcpy.AddField_management(feature_class_name, field_name, field_type)`

---

### 🛠 What You Need to Check First:

Before using the script:

- Make sure your **schema report columns match**: `'Field Name'`, `'Data Type'`, `'Length'` (adjust script as needed).
    
- Check your ArcGIS Pro project has the correct **Geodatabase path**.
    
- Install `pandas` in your Python environment if not already (`conda install pandas` from the ArcGIS Python Command Prompt).
    

---

### 🎁 Bonus Tip: Want to Generate a Schema Template from Existing Feature Class?

If you're starting from an existing feature class and want to copy its schema:

1. Use **"Export > XML Workspace Document"** (with schema only).
    
2. Then use **"Import XML Workspace Document"** to recreate it elsewhere.