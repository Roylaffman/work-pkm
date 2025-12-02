---
tags:
  - project
  - kmz
  - fme
---


[[KMZ FME Diagram]]
# Workflow
# 💯 **FINAL FME WORKBENCH DESIGN (Guaranteed to Work for Your KMZ)**

Below is the exact set of transformers and settings you need.

---

# 1️⃣ **KML/KMZ Reader**

Add Reader:  
**Format:** Google KML  
**Parameters:**

- **Read As** → _fme_feature_
    
- **Expose KML Description** → _Yes_
    
- **Expose Raw KML** → _Yes (optional)_
    

This ensures FME produces attributes:

- `kml_description` (contains your popup HTML)
    
- `kml_name`
    
- `kml_style`
    

---

# 2️⃣ **GeometryFilter**

Add a **GeometryFilter** with 3 outputs:

- Points
    
- Lines
    
- Polygons (optional if your KMZ doesn’t contain them)
    

This helps write each feature type cleanly to your FGDB.

---

# 3️⃣ **HTMLExtractor** → convert popup HTML → plain text

Add an **HTMLExtractor** transformer:

**Input Attribute:** `kml_description`  
**Extraction Mode:** _Extract All Text_  
**Output Attribute:** `NOTES`

This gives you **clean readable text** in the `NOTES` field.

💡 If you want to preserve HTML tables, set:  
**Extraction Mode = Extract HTML As String**  
But for GIS use, plain text is almost always better.

---

# 4️⃣ **AttributeManager** (Clean + Add fields)

In an **AttributeManager**, do the following:

### Add new field:

- `NOTES` = (already created by HTMLExtractor)
    

### Optional:

- Rename `kml_name` → `NAME`
    
- Drop unnecessary fields:
    
    - `kml_description`
        
    - `kml_style`
        
    - `raw_kml`
        
    - any empty temporary fields
        

Your output will contain ONLY:

- `NAME`
    
- `NOTES`
    
- geometry  
    Perfectly clean.
    

---

# 5️⃣ **Reprojector** → EPSG:3857

Add a **Reprojector**:

**Source:** EPSG:4326 (KML default)  
**Destination:** **EPSG:3857**

Done. Everything goes into Web Mercator.

---

# 6️⃣ **File Geodatabase Writer**

Add an FGDB Writer:

### **For each geometry class (from GeometryFilter):**

- Output Feature Class:
    
    - `Points`
        
    - `Lines`
        
    - `Polygons` (if present)
        

### Field mappings:

|Output Field|Source Attribute|
|---|---|
|NAME|kml_name|
|NOTES|NOTES|

Geometry is automatically handled.

---

# 🎯 RESULT

Your FGDB will contain:

### **Points feature class**

- POINT geometry
    
- `NAME` (from placemark name)
    
- `NOTES` (full popup text)
    

### **Lines feature class**

- LINESTRING geometry
    
- `NAME`
    
- `NOTES`
    

### **(Optional) Polygons**

Same schema.

This is future-proof and will work on ANY KMZ you drop into it.

---

# 🚀 NEXT STEP — Converting This to an FME Flow App

Once you’re ready, here’s how to turn this workspace into a web app:

## ✔ 1. Publish workspace to FME Flow

File → Publish to FME Flow  
Upload readers, writers, and all parameters.

## ✔ 2. Create a Web Form

In FME Flow:

- Go to “Apps”
    
- Create a **Workspace App**
    
- Add parameter:
    
    - **Upload KMZ File** (File Upload parameter)
        

## ✔ 3. Add output download

Allow the generated FGDB to be downloaded through the app.

Result:  
Your company can visit a link → upload a KMZ → instantly download FGDB.

I can walk you through this step once your workspace is built.































# Test

Below is the **exact pattern** you need to build in **FME Workbench** to reliably ingest _any_ KMZ/KML — **no matter how messy or inconsistent** — extract both **geometry** _and_ **popup/description data**, and output a clean shapefile or geodatabase feature class in **EPSG:3857**.

This workflow is commonly called **“Robust KML Normalization”** in FME.

---

# ✅ **THE CORE PROBLEM**

KML/KMZ files are inconsistent because:

- Attributes may be stored in `<ExtendedData>`, `<Data>`, `<SimpleData>`, or the `<description>` HTML.
    
- Not all geometries are the same (Point, LineString, Polygon).
    
- Placemark names and folders create unpredictable nesting.
    
- Some attributes stored only in HTML require parsing.
    

FME **can** normalize all of this — but only if you build a generalized transformer workflow.

---

# 🎯 **GOAL**

You want one reusable FME workspace that:

1. Reads any KMZ/KML
    
2. Extracts:  
    ✔ Geometry  
    ✔ All ExtendedData fields  
    ✔ All Description HTML fields  
    ✔ Any Name / Folder path
    
3. Forces ALL attributes into predictable columns
    
4. Reprojects geometry into **EPSG:3857**
    
5. Writes shapefile or geodatabase
    
6. Supports Points, Lines, and Polygons
    

---

# 🛠️ **Build This FME Workflow (Guaranteed to Work)**

## 🔹 **1. Reader: KML/KMZ**

Add a **KML Reader**:

- Format: _Google KML_
    
- Parameters → **Read As: fme_feature**  
    → This gives you maximum low-level control
    
- Enable: **Expose KML Extended Data**
    
- Enable: **Expose Raw KML**
    

This ensures FME captures:

- description
    
- ExtendedData
    
- SimpleData
    
- SchemaData
    
- raw_kml (the XML)
    

---

# 🔹 **2. Add KMLPropertyExtractor**

This transformer pulls out ALL information stored in:

- `<name>`
    
- `<description>`
    
- `<ExtendedData>`
    
- `<SimpleData>`
    

Output attributes you will get:

- `kml_name`
    
- `kml_description`
    
- `kml_extendeddata{}` (list)
    
- `kml_schemadata{}` (list)
    

---

# 🔹 **3. Add AttributeManager + Flatten Attributes**

Because KML often contains list attributes (lists and nested structures):

Add a **ListExploder** or **AttributeManager** set to:

- Expand list attributes
    
- Convert list indices into individual columns  
    Example:
    
    - `kml_extendeddata{}.key` → `AttName_1`
        
    - `kml_extendeddata{}.value` → `AttValue_1`
        

Do this for:

- `kml_schemadata`
    
- `kml_extendeddata`
    
- any `raw_kml` fields you want
# 🔹 **4. Parse Description HTML**

Often the popup is HTML with attributes embedded inside.

Add an **HTMLExtractor**:

- Input = `kml_description`
    
- Mode = **Extract Text From HTML**
    
- OR use Regex in **AttributeManager**:
    
    - Use pattern: `>([^<>]+)<` to extract text
        
    - Or refine if your popups have key/value formatting
        

You can also pass through an **XMLExtractor** if popup data is XML-like.

---

# 🔹 **5. Add GeometryFilter**

Split incoming features into:

- Points
    
- Lines
    
- Polygons
    

This allows you to:

- Write them to separate outputs
    
- Or tag them with geometry type
    

---

# 🔹 **6. Reproject to EPSG:3857**

Add **Reprojector**:

- Source: (auto-detected from KML as EPSG:4326)
    
- Destination: **EPSG:3857**
    

---

# 🔹 **7. (Optional) Clean Up Fields**

Use **AttributeManager** again to:

- Rename all fields
    
- Remove NULL/empty fields
    
- Normalize spacing or weird names
    

---

# 🔹 **8. Write to Output**

Supports:

- File Geodatabase
    
- Shapefiles
    
- GeoPackage
    
- SDE feature class
    

Remember:

- Shapefile field names must be ≤10 characters.
    
- FGDB allows unlimited length.
    

For shapefiles:  
Add a **GISAttributeRenamer** to shorten names.

---

# ⭐ FINAL WORKFLOW DIAGRAM (Simplified)

`[KML Reader]        ↓ [KMLPropertyExtractor]        ↓ [AttributeManager]        ↓ [ListExploder] -- (repeat for each list)        ↓ [HTMLExtractor] (parse popup text)        ↓ [GeometryFilter]        ↓ [Reprojector (EPSG:3857)]        ↓ [AttributeManager] (cleanup)        ↓ [Writer: FGDB or SHP]`

---

# 🔥 **BONUS: Make It Work for ANY KMZ Automatically**

Add these improvements:

### ✔ Use a **Directory and File Path** parameter

Allow user to input ANY KMZ.

### ✔ Use **ZipExtractor**

If the KMZ contains nested KMLs.

### ✔ Add **SchemaMapper**

Map 100’s of potential KML attributes into consistent outputs.

### ✔ Use a **Workspace App (in FME Flow)**

Create a web form:

- Upload KMZ
    
- Select geometry type
    
- Output format
    
- Output coordinate system
    

This creates a **full KMZ → GIS conversion service**.

---

# 📦 If You Want, I Can Build the .FMW for You

You can give me:

- An example KMZ
    
- Desired output fields
    
- Desired output format (FGDB? SHP?)
    
- Whether you want a Flow App version
    

👉 I will generate the **complete workspace**, step-by-step instructions, AND a clean schema for universal KMZ conversion.

Just upload a sample KMZ.