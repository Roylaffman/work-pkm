 


## 🧭 Step 1: Understand What’s Inside the KMZ

Before converting, inspect what you actually have:

- **Folders / subfolders** (e.g., operator, line type, state)
    
- **Geometry mix** (points, lines, polygons)
    
- **Attributes** (names, descriptions, styles)
    

### Quick inspection options

- In **Google Earth Pro**, open the KMZ → expand folders → note the grouping.
    
- In **FME Data Inspector**, open the KMZ → check layers and geometry types.
    

---

## 🧩 Step 2: Choose the Right Output Design

Decide how you want your final data structured.  
Since shapefiles can’t store mixed geometry or nested folders, you’ll want **one shapefile per geometry type per logical group**.

Example:

`Haynesville_Pipelines_Lines.shp Haynesville_Stations_Points.shp Haynesville_Areas_Polygons.shp`

Each file can contain an attribute field called `GroupName` or `FolderPath` so you still know where it came from.

---

## ⚙️ Step 3: Use FME (Best Tool for Complex KMZ → SHP)

Here’s the **reliable workflow** for your case:

### 🧠 Workflow Summary

**Reader:** `KML/KMZ`  
**Transformers:**

- `KMLPropertyExtractor` → extract folder and feature info
    
- `KMLStylerExtractor` (optional) → preserve color/symbols
    
- `AttributeManager` → flatten folder hierarchy into fields like `ParentFolder`, `SubFolder`, `Name`
    
- `GeometryFilter` → split into Point/Line/Polygon streams
    
- `Reprojector` → to desired coordinate system (usually WGS84 EPSG:4326)
    
- `Writer:` `Esri Shapefile`
    

### ✅ Detailed Setup

1. **Open FME Workbench → Add Reader**
    
    - Format: `Google KML`
        
    - Dataset: your KMZ (`Haynesville pipeline.kmz`)
        
    - Check _“Expose all KML properties”_.
        
2. **Add Transformers:**
    
    - `KMLPropertyExtractor` → Extract:
        
        - `kml_parentfolder`
            
        - `kml_subfolder`
            
        - `kml_name`
            
        - `kml_description`
            
    - `AttributeManager` → Rename fields to:
        
        - `Folder1`, `Folder2`, `FeatureName`, `Description`
            
    - `GeometryFilter` → Branch into three outputs:
        
        - `Point`
            
        - `Line`
            
        - `Polygon`
            
    - (Optional) `KMLStylerExtractor` if you want to retain color/style info in attributes.
        
    - (Optional) `Reprojector` → EPSG:4326
        
3. **Add Writers**
    
    - Format: `Esri Shapefile`
        
    - Dataset: folder where you want outputs
        
    - Create one per geometry type, e.g.:
        
        - `Haynesville_Lines.shp`
            
        - `Haynesville_Points.shp`
            
        - `Haynesville_Polygons.shp`
            
4. **Run**
    
    - FME will flatten 1100+ features but keep their folder lineage in the attribute fields.
        
    - You can later use those folder names to symbolize or group in ArcGIS Pro.
        

---

## 🧰 Step 4: Validate and Symbolize in ArcGIS Pro

- Add your three shapefiles into ArcGIS Pro.
    
- Open attribute table — check fields like `Folder1`, `Folder2`, `Name`.
    
- Symbolize or group by those fields to recreate the folder structure visually.
    
- Save as layer files or publish to your enterprise geodatabase.
    

---

## 🚀 Bonus Option (Scripted Alternative if Needed)

If you didn’t have FME, you could use **GDAL/OGR** (built into ArcGIS Pro’s Python environment):

`ogr2ogr -f "ESRI Shapefile" output_folder/lines.shp "Haynesville pipeline.kmz" -nlt LINESTRING ogr2ogr -f "ESRI Shapefile" output_folder/points.shp "Haynesville pipeline.kmz" -nlt POINT ogr2ogr -f "ESRI Shapefile" output_folder/polygons.shp "Haynesville pipeline.kmz" -nlt POLYGON`

This doesn’t preserve folders, but FME can.

---

## ✅ Summary — Best Practice for Complex KMZ

|Goal|Best Tool|Notes|
|---|---|---|
|Preserve folder hierarchy|**FME (KMLPropertyExtractor)**|Flatten into attributes|
|Keep colors/symbols|FME (KMLStylerExtractor)|Optional, may slow down|
|Handle 1100+ subfiles|FME Workbench|Robust, stable|
|Simple conversion (no folders)|ArcGIS Pro “KML to Layer”|Quick but loses structure|
|Scripted batch|GDAL/OGR|Good fallback|

---

### 🔧 Recommended Final Output

`/Haynesville_Export/   Haynesville_Points.shp   Haynesville_Lines.shp   Haynesville_Polygons.shp   Haynesville_Metadata.csv   (optional)`

Each record has:

- `Folder1`
    
- `Folder2`
    
- `Name`
    
- `Description`
    
- `Geometry`
    

---

If you’d like, I can write a ready-to-load **FME workspace (.fmw)** that does all this (including geometry filtering, flattening folder structure, and output to 3 shapefiles).  
Would you like me to outline or script that exact FME workflow for your KMZ?