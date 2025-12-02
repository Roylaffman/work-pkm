---
tags:
  - requests
---



### 🧭 **Summary of Request**

The GIS team needs to **update the Exxon to Betty Barney route** and **add riser features** (two risers) to the appropriate locations. Once risers are added and named, the **Northcutt line’s status** must be updated according to the section it belongs to.

---

### 🛠️ **Tasks to Complete**

#### **1. Pipeline Update**

- Locate the **Exxon to Betty Barney route** in the NM asset data.
    
- Review the current geometry and **update or replace the route line** with the new pipeline provided in the request (the new pipeline file that came with the risers).
    
- Ensure the updated line maintains proper connectivity with the existing network (especially near Northcutt).
    

#### **2. Add the Two Riser Features**

- Import and place the **two risers** that came with the request into the appropriate feature class (likely under _Risers_ or _Pipeline Equipment_ in the enterprise geodatabase).
    
- Ensure the risers:
    
    - Are snapped to the pipeline.
        
    - Include proper **attributes** such as:
        
        - `Name` (provided in attachment or naming convention)
            
        - `Status`
            
        - `Connected Pipeline`
            
        - `Operator` (if applicable)
            
    - Have spatial accuracy (check coordinates or match pipeline alignment).
        

#### **3. Attribute and Status Updates**

- Once risers are correctly added:
    
    - Update **Northcutt pipeline status**:
        
        - **From the connection to Riser 7 northward:** set status to **Construction**.
            
        - **South of Riser 7:** set status to **Proposed**.
            
- Ensure attributes such as `Status`, `ProjectName`, `Segment`, and `UpdatedBy` are filled out consistently.
    

#### **4. Quality Check**

- Validate topology: ensure no gaps, overlaps, or dangles between the updated pipeline and risers.
    
- Verify all naming conventions follow Select Water GIS standards.
    
- Run validation in ArcGIS Pro (Topology Checker or Attribute Rules if configured).
    

#### **5. Documentation**

- Add a note or update in the **Service Desk (SD)** ticket:
    
    - Reference: “Exxon to Betty Barney and Coterra Risers update – pipeline and riser integration complete, Northcutt status updated per instruction.”
        
- Attach updated MXD or project file if applicable.
    
- Reassign or close the SD once QC and approval are complete.
    

---

### 👷 Assigned Roles

- **Primary:** Nick Hodgins (as Priority 2 task)
    
- **Reviewer:** Natalie Foster
    
- **Created by:** Jeremy Rangel