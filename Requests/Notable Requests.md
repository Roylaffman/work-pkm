#3319


We have a riser "Select-Riser-0049" at the KMZ point Named "Select Riser" from LT with this Photo:


We have Select-Riser-0125 From Hectors KMZ: "Riser 2"


There is a nearby booster station labeled "SWD Booster Station at Pakse to Jewett" at this location as well according to our GIS Database





### 🎯 What You’re Being Asked To Do

- **Check the location and attributes of the Deep Purple Riser** in GIS.
    
- **Compare the KMZ pins** from Hector and LT Survey to confirm:
    
    - Which riser (Riser 1, Riser 2, or Select Riser) is actually the “Deep Purple Riser.”
        
    - Whether the riser has available tie-in points for the Roberne pipeline.
        
- **Update maps and confirm coordinates** so Ricci can proceed with the survey update and BLM permit amendment.
    
- Optionally, note whether the **booster station is still active** or available for reuse.
    

---

### 🧭 Action Steps for You

1. Download/open the KMZ files attached in Ricci’s email.
    
2. Load them into ArcGIS Pro or Google Earth.
    
3. Verify the locations of:
    
    - Riser 1, Riser 2, Select Riser, and any “Deep Purple Riser.”
        
4. Compare those with current Select infrastructure layers.
    
5. Identify which riser corresponds to “Deep Purple.”
    
6. Reply to confirm coordinates and tie-in options for engineering and land teams.

# Links 
Resource buffer zones for permitting SSPS and ENV
[Data Eng & Analytics - Processes - All Documents](https://selectenergyservices.sharepoint.com/sites/Corp-DEA/Shared%20Documents/Forms/AllItems.aspx?id=%2Fsites%2FCorp%2DDEA%2FShared%20Documents%2FProduction%2FGIS%2FDesktop%20Review%2FProcesses&viewid=a2f91a63%2Dec78%2D4d8d%2Da9bf%2D53a1ee5bb35c&p=true&ga=1)
# 3351
### **Summary: Pipeline Naming Updates (Ticket ##3351##)**

**Participants:**  
Jeremy Rangel, Guy Chamberlin, Ross Lera, Belinda Hitchcock, Brandon Prill, Natalie Foster, and the GIS Requests team.

**Topic:**  
Clarification and standardization of naming conventions for pipelines in the **Permian region**, specifically what has been informally referred to as the **“Northern Line.”**

**Key Points:**

- **Jeremy Rangel** initiated the conversation to clarify naming for the “Northern Line.”
    
    - The grant lists it under “Earthstone and RF Pipelines.”
        
    - He emphasized avoiding **customer names**, **the words “Pipeline” or “Lines”**, and **descriptive terms** (e.g., “Produced Water,” “20-inch”).
        
    - The area includes **four sets of dual lines**.
        
- **Ross Lera** looped in **Brandon Prill** for input.
    
- **Brandon Prill** suggested keeping the name simple as **“Northern”**, but expressed confusion about removing “Pipeline” from naming conventions.
    
- **Guy Chamberlin** raised a question about the impact of renaming, asking if **811 data updates** would be required since many current names match permit names.
    
- **Jeremy Rangel** clarified that **811 data does not include asset names**, so **no update is required** there.
    

---

### **Tasks to Complete**

1. **Finalize Naming Convention**
    
    - Confirm agreement across stakeholders (Jeremy, Ross, Guy, Brandon, Belinda) on the finalized name for the former “Northern Line.”
        
    - Document the decision — likely **“Northern”** — in the pipeline asset registry.
        
2. **Update GIS Database**
    
    - Apply the new standardized name to the relevant pipelines in GIS datasets (Permian region).
        
3. **Verify Naming Consistency**
    
    - Review all related **pipeline datasets** and **maps** to ensure consistent naming.
        
    - Confirm no mismatches exist between GIS, permit, and asset management systems.
        
4. **Document Policy**
    
    - Update internal **naming conventions documentation** to clarify:
        
        - Customer names and descriptive labels are not to be used.
            
        - The term “Pipeline” may be excluded unless otherwise approved.





# 3797

## 🧭 Step 1. Prepare the Excel Schema

1. In Excel, make sure your first row contains **field names** — for example:
    
    `DiversionID, WDID, USGS_MonLoc_Num, Name, Site_Type, State, County, HUC, Elevation, Status, Comments`
    
2. Ensure each column has a clear and consistent **data type**:
    
    - Text → for IDs, names, status, comments
        
    - Double → for numeric values like elevation
        
    - Date → for installation dates if included
        
3. Save the file as:
    
    `Diversion_Headgate_Schema.xlsx`
    

---

## 🗂️ Step 2. Import Excel into ArcGIS Pro

1. In **ArcGIS Pro**, open your project and **Catalog Pane** (`View > Catalog Pane`).
    
2. Right-click your **geodatabase** (for example: `Default.gdb` or `EGIS.gdb`) → **Import > Table**.
    
3. Browse to your Excel file → choose the sheet containing your schema (e.g., `Sheet1$`).
    
4. Name it `DiversionSchema_Table`.
    
5. Once imported, right-click → **Design > Fields**.
    
6. Verify data types:
    
    - Text = 255 length
        
    - Doubles for numeric
        
    - Add aliases (optional) for user-friendly names.
        

---

## 🧩 Step 3. Create the Feature Class from Schema

1. In **Catalog Pane**, right-click your target **Geodatabase** → **New > Feature Class**.
    
2. Name it:
    
    `Diversion_Headgates`
    
3. Geometry Type: **Point**
    
4. Coordinate System: Use **WGS 1984 (EPSG:4326)** for compatibility with USGS/CDSS coordinate data.
    
5. In the **Fields** setup screen:
    
    - Click **Import** → select your `DiversionSchema_Table`.
        
    - This will automatically populate all your columns.
        
6. Finish and click **Run**.
    

✅ You now have an **empty point feature class** with your schema.

---

## 🌐 Step 4. Add USGS and CDSS Points

### **Option A: Using CSV/GeoJSON (Recommended)**

#### 4.1 Download or Request Data

- From **USGS API**, request monitoring locations (example in browser or script):
    
    `https://api.waterdata.usgs.gov/ogcapi/v0/collections/monitoring-locations/items?f=geojson&bbox=-109,36,-101,41`
    
    _(Adjust bbox to your area of interest.)_
    
- From **CDSS**, download diversion points from:  
    https://dwr.state.co.us/tools/maps/structures/  
    → Export as CSV or shapefile.
    

#### 4.2 Add to ArcGIS Pro

1. **Add Data → Add XY Event Layer** (for CSVs with coordinates).
    
    - X Field: Longitude
        
    - Y Field: Latitude
        
    - CRS: WGS 1984
        
2. **Export** the layer to your Geodatabase:
    
    - Right-click layer → **Data > Export Features**
        
    - Name it `USGS_Monitoring_Points` or `CDSS_Diversions`.