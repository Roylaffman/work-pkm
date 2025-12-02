---
tags:
  - request
  - drone
  - portal
---

# Drone Stuff - 3647

### **Key Points by Participant**

### **Jeremy Pelto**

- Initiated the discussion with **Permian Resources** about routing and installation plans for the Pinky Pie and Long John Silver tie-ins.
- Shared annotated **drone photos** showing aboveground and buried line routes, as well as skid placements.
- Offered to fly a drone for updated imagery to confirm meter run locations, pending permission.

### **Permian Resources (Davro Clements & Alex Kirkendall)**

- Confirmed **connection points** for Select’s lines and **approved excavation without on-site supervision**.
- Requested visuals of proposed locations, providing example files for reference.

### **Terry Caldwell**

- Emphasized the **value of drone and satellite imagery** for early project coordination with customers.
- Suggested **KMZ-formatted drone images** for use in **Google Earth**, citing examples Jeremy had already created.
- Acknowledged drone altitude limitations but noted the usefulness of overhead imagery for showing site context.

### **Fred Brown**

- Agreed drone imagery is preferred, noting **satellite imagery with similar resolution is cost-prohibitive**.
- Stated drones can provide accurate enough visuals for planning and design, though not survey-grade.
- Recommended using **stitched drone images** and integrating them into **GIS** for internal planning.

### **Nathan Askins**

- Flagged that Terry’s request for **KMZ files** might conflict with the team’s preferred data workflow.
- Suggested discussing or clarifying with Terry to align on proper **GIS-compatible formats** instead of KMZs.
- Noted that **Ryan Lafferty and Jeremy Pelto** had been developing an efficient **drone-to-GIS integration process**, with plans to share more information soon.

---

### **Outcome**

- Consensus: **Drone imagery** is the best tool for visual tie-in planning and GIS integration.
- **Next steps:** Nathan, Ryan, and Jeremy will refine and share a **workflow for importing drone imagery into GIS**.
- Action item: **Clarify acceptable file formats** (KMZ vs. GIS-native) with Terry Caldwell to streamline data use across teams.

---

### **Core Takeaway**

The Select Water Solutions team is aligning on a **standardized approach to incorporating drone imagery** into GIS for tie-in planning. The focus is on practicality, efficiency, and format consistency — using **drone orthophotos** rather than KMZs where possible to ensure smooth GIS integration. 3647 Continued 

## 🧩 Part 1: Understanding Tie-ins, Meter Runs, and Pump Skids in the Midstream Water Business

### **1. Tie-Ins**

**Definition:**  
A _tie-in_ is a **connection point** between two sections of pipeline or facilities — for example, where a new line from a customer’s tank battery (CTB) or facility connects to Select’s permanent water infrastructure (gathering, disposal, or transfer line).

**Purpose:**

- Enables the flow of water between different operators, systems, or facilities.
- Often used when integrating temporary flowlines or connecting new production pads to the main system.

**In the field:**

- Tie-ins are usually **buried** connections, designed to handle permanent flow safely.
- Engineers or PMs identify the _“take point”_ where Select’s pipeline meets the customer’s discharge point.
- Drone imagery is used to **confirm tie-in locations** visually for approvals before excavation.

---

### **2. Meter Runs**

**Definition:**  
A _meter run_ is a **short section of pipe containing flow meters and valves** used to measure the volume and rate of water flow entering or leaving a system.

**Purpose:**

- Provides accurate measurement for **billing, balancing, and operations**.
- Usually includes:
    - A **flow meter** (e.g., ultrasonic or turbine)
    - **Valves** for isolation and maintenance
    - **Pressure/temperature sensors**

**In Select’s context:**

- Meter runs are typically **mounted on a skid** (a prefabricated frame) and connected between the customer tie-in and the permanent line.
- They’re pre-built (“pre-fab skids”) and then “dropped in place” in the field to reduce downtime.

---

### **3. Pump Skids**

**Definition:**  
A _pump skid_ is a **modular, self-contained pumping system** mounted on a steel skid for easy transport and setup.

**Purpose:**

- Used to **boost water pressure** in transfer lines, especially over long distances or to overcome elevation gain.
- Common in midstream water networks for:
    - Moving produced water to disposal wells
    - Moving treated/recycled water to new drill sites

**Components:**

- One or more **centrifugal pumps** or **progressive cavity pumps**
- **Motor and VFD (Variable Frequency Drive)**
- **Valving and manifold systems**
- **Flow meters and sensors**

**In practice:**

- Facilities like “Pinky Pie” or “Long John Silver” might each have pump skids where water enters the mainline.
- GIS teams map these **as fixed infrastructure points** with attributes like pump type, flow capacity, and connection type.

---

### **How They All Work Together**

Imagine this simplified workflow:

```
[Customer CTB / Facility]  
        │  
     (Tie-In)  
        │  
[Meter Run Skid]  
        │  
[Pump Skid (Boosts Flow)]  
        │  
[Select Mainline → Disposal/Recycle Facility]
```

Each step provides **connectivity, measurement, and control** for the movement of water through the system.

---

## 🛰️ Part 2: Hosting Orthophotos in PostgreSQL / EGIS without Image Services

Since Select’s EGIS is **ESRI-based** but you don’t currently use **Image Server / Raster Server**, you can still host and visualize drone orthophotos internally using **Portal and PostgreSQL (PostGIS)** with the latest ESRI capabilities.

### **1. What Changed - Infrastructure**

Recent ArcGIS Enterprise updates (v11.2 and up) introduced better support for:

- **Raster data in PostGIS**
- **Hosted imagery layers in Portal** without the need for Image Server extensions
- **Cloud raster store support (via raster datasets or .tif mosaic datasets)**

That means you can now:

- Store orthophoto **tiles or mosaics** directly in PostgreSQL
- Reference them in **Portal via mosaic datasets**
- Publish as **dynamic or tiled imagery layers**

---

### **2. Hosting Options**

### **Option A: Store Orthophotos in PostgreSQL as Raster Columns**

**How:**

- Use **PostGIS raster type** (`ST_Raster`)
- Import orthophotos (.tif, .jpg, etc.) using `raster2pgsql` or ArcGIS Pro’s “Copy Raster to Database” tool
- Store each image with its **spatial reference and extent**

**Pros:**

- Centralized with your EGIS data
- Fully queryable (you can clip, sample, or overlay in SQL or GIS)
- Fast retrieval if indexed properly

**Cons:**

- Large file sizes = big database growth
- Requires careful tile management for performance

---

### **Option B: Host via Portal as a Tiled Imagery Layer**

**How:**

1. Upload georeferenced drone imagery (.tif, .jpg) into a **folder or raster dataset**.
2. Create a **Mosaic Dataset** in ArcGIS Pro.
3. Publish to **Portal for ArcGIS** as a **Hosted Imagery Layer** (tiled).
    - You can set cache levels for zoom (for example, 1:10,000 to 1:500).

**Pros:**

- Lightweight for users
- Good for orthophotos viewed on web maps
- No Image Server license required (as of Enterprise 11.2+)

**Cons:**

- Not editable directly in Portal
- Limited analytic functions compared to full Image Server

---

### **Option C: Hybrid Approach**

- Store raw raster data in **PostgreSQL (PostGIS)**
- Reference or mosaic it in **ArcGIS Pro**
- Publish to Portal as **cached or dynamic map service**

This gives you the flexibility to:

- Keep data in your database (under your control)
- Serve visualization layers in Portal
- Allow analysts to query / symbolize via EGIS

---

### **3. Recommended Workflow for You**

Since you already manage layers and facilities in EGIS and Portal:

1. **Prepare the Orthophotos**
    - Ensure all drone images are orthorectified and georeferenced (.tif preferred)
    - Maintain consistent projection (likely EPSG: 3857 or state plane)
2. **Load into Postgres**
    - Use ArcGIS Pro’s _“Copy Raster to Database”_ tool  
        → Target: Your enterprise PostgreSQL connection  
        → Output: A raster dataset in a new schema (e.g., `imagery`)
3. **Create Mosaic Dataset**
    - In ArcGIS Pro, create a new mosaic dataset referencing the database rasters
    - Build footprints and overviews
4. **Publish to Portal**
    - Share as a **Hosted Tile Layer** (for visualization)
    - Add metadata (date captured, resolution, operator, site name)
5. **Use Bookmarks / Web Maps**
    - Integrate these hosted orthophotos into Portal web apps or EGIS dashboards
    - Link to your pipeline, tie-in, and meter run layers for interactive context

---

### **4. Bonus Tip**

If you want field crews to access these orthophotos offline, use:

- **ArcGIS Field Maps with offline areas**, or
- Export to **MBTiles** using ArcGIS Pro “Create Tile Package (TPKX)” and sideload it to devices.

[Customer CTB / Facility]  
        │  
     (Tie-In)  
        │  
[Meter Run Skid]  
        │  
[Pump Skid (Boosts Flow)]  
        │  
[Select Mainline → Disposal/Recycle Facility] ## 🛰️ Step-by-Step Guide: Hosting Drone Orthophotos in PostgreSQL and Publishing to Portal

---

### 🧱 **Step 1: Prepare the Orthophotos**

**Goal:** Make sure your drone imagery is georeferenced, orthorectified, and ready for import.

### ✅ Requirements:

- File format: `.tif` (preferred), `.jpg` with world file (`.jgw`) also works.
- Projection: Use **Web Mercator (EPSG: 3857)** or **NAD83 StatePlane** (depending on your EGIS schema).
- Resolution: Ideally between **2–10 cm/pixel**.
- Naming: Use clear naming like

### 🛠️ Optional prep:

In **ArcGIS Pro**:

1. Use **Data Management → Projections → Project Raster**  
    → Ensure your projection matches your enterprise data.
2. Clip to your area of interest (AOI) with **Raster Clip**.
3. Save final `.tif` files locally before uploading.

---

### 🧩 **Step 2: Connect to PostgreSQL in ArcGIS Pro**

1. Open **ArcGIS Pro**.
2. Go to the **Catalog pane → Databases**.
3. Right-click and choose **New Database Connection**.
4. Fill in:
    - **Database Platform:** PostgreSQL
    - **Instance:** your PostgreSQL host name or IP
    - **Authentication:** Database or OS (depending on your EGIS setup)
    - **Database:** your enterprise GIS database (e.g. `select_egis`)
    - Save the connection file (it will appear under _Databases_ in the Catalog).

---

### 🗺️ **Step 3: Load the Orthophotos into PostgreSQL**

**Tool:** `Copy Raster to Database`  
**Path:** _Data Management Tools → Raster → Raster Dataset → Copy Raster to Database_

### Input Parameters:

|Parameter|Description|
|---|---|
|**Input Raster**|Your georeferenced `.tif` file (e.g. `PinkyPie_20250725_orthophoto.tif`)|
|**Target Geodatabase**|Your PostgreSQL connection (from Step 2)|
|**Raster Name**|A short, clear name (e.g. `pinky_pie_orthophoto`)|
|**Spatial Reference**|Same as your enterprise database projection|
|**Compression Type**|`LZ77` (balance of quality + storage)|

Run the tool.  
Repeat for each orthophoto.

You’ll now have one raster dataset per site stored **inside PostgreSQL**.

---

### 🗂️ **Step 4: Create a Mosaic Dataset**

**Why:** Mosaic Datasets let you combine multiple orthophotos (tiles, dates, or areas) into a single dynamic layer.

1. In ArcGIS Pro, go to **Catalog → Databases → your PostgreSQL connection**.
2. Right-click your target schema (e.g. `imagery`) → **New → Mosaic Dataset**.
3. Give it a name, e.g. `drone_orthos_mosaic`.
4. Choose the same **coordinate system** as your raster data.
5. Click **OK**.

Now:

- Right-click the new mosaic dataset → **Add Rasters**
    - Choose _Raster Datasets_
    - Navigate to your PostgreSQL connection
    - Select all orthophotos
    - Check _Update Cell Size Ranges_, _Build Overviews_.

Once built, ArcGIS will display them as one seamless image layer.

---

### 🌐 **Step 5: Publish to Portal (No Image Server Needed)**

Since ArcGIS Enterprise 11.2+, you can publish imagery as a **Hosted Tile Layer** directly from ArcGIS Pro.

### 1. Sign in to Portal

- Go to **Share → Sign In → Your Organization Portal** ([https://portal.selectwater.com/portal](https://portal.selectwater.com/portal "https://portal.selectwater.com/portal"), likely).

### 2. Right-click the Mosaic Dataset → **Share As Web Layer**

- **Layer Type:** _Tile Layer_
- **Configuration:** choose “Tiled Cache”
- **Layer Name:** e.g. `Drone_Orthos_Permian_2025`
- **Folder:** `Imagery` or `Field_Operations`

### 3. Under _Configuration_ tab:

- Set visible scales: e.g. 1:100,000 → 1:500
- Enable _Allow clients to export cache tiles_ (optional).
- Optionally add a _feature boundary_ layer for site outlines.

### 4. Click **Analyze → Publish**

ArcGIS Pro will create:

- A **Hosted Tile Layer** in Portal
- Cached tiles stored in your Enterprise Data Store (not in Image Server)

Now you can open the hosted layer in **Map Viewer**, **Field Maps**, or **EGIS web apps**.

---

### 🔗 **Step 6: Link Orthophotos to Asset Layers**

To integrate with existing EGIS data:

1. Open your main facilities or pipelines web map in Portal.
2. Add the new orthophoto tile layer.
3. Optionally, configure a pop-up or link in your **tie-in** or **facility** layer:
    - e.g. “View Orthophoto” → opens your hosted imagery layer.

---

### 📁 **Step 7: Maintain and Update**

When new drone imagery arrives:

- Import the new `.tif` to PostgreSQL (Step 3)
- Add it to the existing mosaic (Step 4)
- Rebuild overviews
- Republish or update the hosted tile cache (Step 5)

No need to delete or recreate layers — just update the mosaic dataset.

---

## ⚙️ Bonus: Example Folder Structure

```
\\GIS_Drone_Imagery\│├── 2025_07_25_PinkyPie\│   ├── PinkyPie_orthophoto.tif│   ├── PinkyPie_AOI.shp│├── 2025_07_25_LongJohnSilver\│   ├── LongJohnSilver_orthophoto.tif│   ├── LongJohnSilver_AOI.shp│└── 2025_07_25_Metadata\    ├── metadata_PinkyPie.xml    ├── metadata_LongJohnSilver.xml
```

---

## 🧭 Summary

|Step|Tool|Result|
|---|---|---|
|1|Project Raster|Uniform coordinate system|
|2|PostgreSQL Connection|Database ready|
|3|Copy Raster to Database|Orthophoto stored in Postgres|
|4|Create Mosaic Dataset|Manage imagery collection|
|5|Share as Web Layer|Hosted tile layer on Portal|
|6|Add to Web Maps|Integration with EGIS|
|7|Update Mosaic|Maintain versioned imagery|

---

If you’d like, I can write a **short SQL + ArcPy automation script** that automatically ingests new drone orthophotos into your PostgreSQL and updates the mosaic dataset when new images are added to a folder (ideal for recurring drone flights).

```
PinkyPie_20250725_orthophoto.tifLongJohnSilver_20250725_orthophoto.tif 
```  Last Python code for addign OPrtho Photos: add all this to Obsidian:

Awesome — here’s a **turn-key ArcPy automation** that will:

1. scan a “drop” folder for new orthophoto TIFFs
2. (optionally) project them to your enterprise CRS
3. copy each raster into your **PostgreSQL Enterprise Geodatabase (EGDB)**
4. add/update a **Mosaic Dataset** (MD) that manages all drone imagery
5. publish/update a **Hosted Tile Layer** in **Portal** (no Image Server required)

Everything is in one script with clearly marked settings. Drop it on a server with ArcGIS Pro/Enterprise utilities installed and schedule via Task Scheduler.

---

# 1) ArcPy script (save as `ingest_drone_orthos.py`)


# -*- coding: utf-8 -*- # ingest_drone_orthos.py 


```


# Automates ingest of drone orthophotos to PostgreSQL EGDB, updates mosaic dataset, # and publishes/refreshes a hosted tile layer in Portal (no Image Server needed). # # Run with ArcGIS Pro's Python (conda) environment. # Example: "C:\Program Files\ArcGIS\Pro\bin\Python\Scripts\propy.bat" ingest_drone_orthos.pyimport osimport sysimport timeimport globimport arcpyfrom datetime import datetime# ---------------------------- # USER SETTINGS (EDIT THESE) # ---------------------------- # Folder to watch for new orthos (.tif). Subfolders supported.ORTHO_INBOX_FOLDER = r"\\fileserver\GIS_Drone_Imagery\inbox"# Where to write temporary projected rasters (local SSD is best)WORK_FOLDER = r"D:\gis_work\drone_ingest\work"# Enterprise PostgreSQL connection (.sde) created from ArcGIS Pro -> New Database ConnectionEGDB_CONN = r"\\appserver\connections\select_egis_prod.sde"  # e.g. ...\select_egis_prod.sde# Target EGDB dataset/schema to hold rasters & mosaic datasetTARGET_DATASET = r"{}\imagery".format(EGDB_CONN)  # optionally an existing feature dataset (for MD) or just EGDB root # Mosaic Dataset name to manage all drone imagery (created if missing)MOSAIC_NAME = "drone_orthos_mosaic"# Enterprise coordinate system for all imagery (match pipelines/base maps) # e.g., 3857 (Web Mercator), or a StatePlane WKIDTARGET_WKID = 3857# Portal info (user must have privileges to publish Hosted Tile Layers)PORTAL_URL = "https://portal.selectwater.com/portal"PORTAL_USER = "your.portal.username"PORTAL_PWD  = "your-APP-PASSWORD-or-token"   # Prefer an App Registration / PAT if you have it# Hosted Tile Layer service name (created on first publish; updated on subsequent runs)SERVICE_NAME = "Drone_Orthos_Permian"# Min/Max scale for cached tiles (Web Mercator sample levels) # Example: ~1:100k to 1:500 (adjust to your needs)SCALES = [577790.554289, 288895.277144, 144447.638572, 72223.819286, 36111.909643, 18055.954822, 9027.977411, 4513.988705, 2256.994353, 1128.497176, 564.248588]# Metadata / tagsSERVICE_SUMMARY = "Hosted drone orthophotos (mosaic) for Permian facilities and tie-ins."SERVICE_TAGS = "drone,orthophoto,imagery,permian,select water,EGIS"# File pattern to ingestGLOB_PATTERN = "**/*.tif"# Log fileLOG_PATH = r"D:\gis_work\drone_ingest\logs\ingest.log"# If True, project input rasters to TARGET_WKID before copy to EGDB.DO_PROJECT = True# Optional: skip files smaller than N MB to avoid trash filesMIN_SIZE_MB = 5# ---------------------------- # END USER SETTINGS # ----------------------------def log(msg):    ts = datetime.now().strftime("%Y-%m-%d %H:%M:%S")    line = f"[{ts}] {msg}"    print(line)    try:        os.makedirs(os.path.dirname(LOG_PATH), exist_ok=True)        with open(LOG_PATH, "a", encoding="utf-8") as f:            f.write(line + "\n")    except Exception:        passdef ensure_folder(path):    if not os.path.isdir(path):        os.makedirs(path, exist_ok=True)def sign_in_portal():    arcpy.SignInToPortal(PORTAL_URL, PORTAL_USER, PORTAL_PWD)    log(f"Signed in to Portal: {PORTAL_URL} as {PORTAL_USER}")def list_tifs():    pattern = os.path.join(ORTHO_INBOX_FOLDER, GLOB_PATTERN)    return [p for p in glob.glob(pattern, recursive=True) if p.lower().endswith(".tif")]def size_mb(path):    try:        return os.path.getsize(path) / (1024.0 * 1024.0)    except Exception:        return 0def project_raster(in_raster, out_folder, wkid):    ensure_folder(out_folder)    base = os.path.splitext(os.path.basename(in_raster))[0]    out_raster = os.path.join(out_folder, f"{base}_prj.tif")    sr = arcpy.SpatialReference(wkid)    log(f"Projecting raster → {out_raster} (WKID {wkid})")    arcpy.management.ProjectRaster(        in_raster=in_raster,        out_raster=out_raster,        out_coor_system=sr,        resampling_type="NEAREST",        cell_size=None,        geographic_transform="",        Registration_Point=""    )    return out_rasterdef copy_raster_to_egdb(in_raster, egdb_conn, out_name):    # Output must be a dataset path inside EGDB    out_path = os.path.join(egdb_conn, out_name)    log(f"Copying raster to EGDB → {out_path}")    # Using Copy Raster writes ArcGIS-managed raster to EGDB    arcpy.management.CopyRaster(        in_raster=in_raster,        out_rasterdataset=out_path,        config_keyword="",        background_value="",        nodata_value="",        onebit_to_eightbit="NONE",        colormap_to_RGB="NONE",        pixel_type="",        scale_pixel_value="NONE",        RGB_to_Colormap="NONE",        format="",        transform="NONE"    )    return out_pathdef ensure_mosaic(egdb_dataset, mosaic_name, wkid):    mosaic_path = os.path.join(egdb_dataset, mosaic_name)    if not arcpy.Exists(mosaic_path):        log(f"Creating Mosaic Dataset: {mosaic_path}")        arcpy.management.CreateMosaicDataset(            in_workspace=egdb_dataset,            in_mosaicdataset_name=mosaic_name,            coordinate_system=arcpy.SpatialReference(wkid)        )        # Optional: define default fields/properties here as neededelse:        log(f"Mosaic Dataset exists: {mosaic_path}")    return mosaic_pathdef add_rasters_to_mosaic(mosaic_path, rasters_in_egdb):    if not rasters_in_egdb:        log("No rasters to add to mosaic.")        return    log(f"Adding {len(rasters_in_egdb)} raster(s) to mosaic...")    arcpy.management.AddRastersToMosaicDataset(        in_mosaic_dataset=mosaic_path,        raster_type="Raster Dataset",        input_path=";".join(rasters_in_egdb),        update_cellsize_ranges="UPDATE_CELL_SIZES",        update_boundary="UPDATE_BOUNDARY",        update_overviews="NO_OVERVIEWS",        maximum_pyramid_levels="",        maximum_cell_size="",        minimum_dimension="",        spatial_reference="",        filter="",        sub_folder="SUBFOLDERS",        duplicate_items_action="EXCLUDE_DUPLICATES"    )    log("Building overviews...")    arcpy.management.BuildOverviews(mosaic_path)def publish_or_update_hosted_tile(mosaic_path):    # Build a temporary map and share as Hosted Tile Layer    log("Preparing sharing draft for Hosted Tile Layer…")    aprx = arcpy.mp.ArcGISProject("CURRENT") if arcpy.Exists("CURRENT") else arcpy.mp.ArcGISProject("CIMPATH=INMEMORY")    m = aprx.createMap("DroneImageryPublishMap")    lyr = arcpy.mp.LayerFile(None)    # Add the mosaic dataset as a layer    m.addDataFromPath(mosaic_path)    draft = arcpy.sharing.CreateSharingDraft(        server_type="HOSTING_SERVER",        service_type="TILE",        service_name=SERVICE_NAME,        draft_value=m    )    # Item metadata    draft.summary = SERVICE_SUMMARY    draft.tags = SERVICE_TAGS    draft.portalFolder = "Imagery"  # optional folder name in Portal content# Tile cache settings (scales)    draft.exportTileCache = True    draft.copyDataToServer = True    draft.minScale = min(SCALES)    draft.maxScale = max(SCALES)    draft.offline = True# Stage & upload    sd = os.path.join(WORK_FOLDER, f"{SERVICE_NAME}.sd")    ensure_folder(WORK_FOLDER)    log("Staging service definition…")    arcpy.sharing.StageService(draft, sd)    log("Uploading service definition (this builds/updates the cache)…")    arcpy.sharing.UploadServiceDefinition(sd)    log(f"Hosted Tile Layer '{SERVICE_NAME}' published/updated.")def main():    try:        ensure_folder(WORK_FOLDER)        sign_in_portal()        # Find new orthos        candidates = list_tifs()        log(f"Found {len(candidates)} TIFF(s) under inbox.")        # Filter tiny files        tifs = [p for p in candidates if size_mb(p) >= MIN_SIZE_MB]        if len(candidates) != len(tifs):            log(f"Skipped {len(candidates)-len(tifs)} file(s) smaller than {MIN_SIZE_MB} MB.")        if not tifs:            log("No usable TIFFs found. Exiting.")            return# Ensure mosaic dataset        mosaic_path = ensure_mosaic(TARGET_DATASET, MOSAIC_NAME, TARGET_WKID)        # Ingest each TIFF        egdb_rasters = []        for tif in tifs:            try:                # Project if requested                src = tif                if DO_PROJECT:                    src = project_raster(tif, WORK_FOLDER, TARGET_WKID)                # Create short, DB-safe name: site_date_orthophoto                base = os.path.splitext(os.path.basename(tif))[0]                safe = "".join([c if c.isalnum() or c in ("_",) else "_" for c in base]).lower()                out_name = safe  # ensure uniqueness via naming convention upstream# Copy to EGDB                egdb_raster = copy_raster_to_egdb(src, EGDB_CONN, out_name)                egdb_rasters.append(egdb_raster)            except Exception as e:                log(f"ERROR ingesting {tif}: {e}")        # Update mosaic        add_rasters_to_mosaic(mosaic_path, egdb_rasters)        # Publish or update hosted tile        publish_or_update_hosted_tile(mosaic_path)        log("Ingest workflow completed successfully.")    except Exception as e:        log(f"FATAL ERROR: {e}")        raiseif __name__ == "__main__":    main()
```

---

## 2) What to prepare once (one-time setup)

- **Create the .sde connection** used by `EGDB_CONN` (save credentials).
- **Create (or choose) a schema/dataset** in EGDB to hold imagery & the Mosaic Dataset:
    - If you don’t already have `imagery`, make it in ArcGIS Pro (feature dataset not required, but organizing helps).
- **Create a Portal account/credential** that can publish Hosted Tile Layers (Publisher/Creator role or higher).
- **Pick your CRS** (Web Mercator or your StatePlane). Update `TARGET_WKID`.
- **Create the WORK & LOG folders** (fast local disk preferred).

> Note: This script uses ArcGIS’ native raster storage inside the Enterprise Geodatabase (PostgreSQL). No PostGIS raster type is required.

---

## 3) How to run it

**Manual (for testing):**

```
"C:\Program Files\ArcGIS\Pro\bin\Python\Scripts\propy.bat" D:\gis_work\drone_ingest\ingest_drone_orthos.py
```

**Schedule (Windows Task Scheduler):**

- Action → Start a program
- Program/script:
- Add arguments:
- Start in:
- Trigger: daily, or every hour during operations.

---

## 4) Operational notes & tuning

- **Scales (**`**SCALES**`**)**: narrow to what you need; fewer/larger scales ⇒ faster cache builds.
- **Names/uniqueness**: keep consistent TIFF naming (e.g., `Site_YYYYMMDD_orthophoto.tif`) so the copied raster name is stable.
- **Re-runs/updates**: the script _adds_ rasters and _rebuilds overviews_; Portal publish step will **update** the same Hosted Tile Layer name.
- **Performance**: put `WORK_FOLDER` on SSD; run off-hours for heavy cache updates.
- **Security**: prefer a **Portal app token** or PAT rather than a raw password.

```
D:\gis_work\drone_ingest 
```

```
D:\gis_work\drone_ingest\ingest_drone_orthos.py 
```

```
C:\Program Files\ArcGIS\Pro\bin\Python\Scripts\propy.bat  
```