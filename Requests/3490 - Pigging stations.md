### Ideas for each type


|Component|Description|Common Use in Midstream Water Systems|Typical GIS Feature Type|
|---|---|---|---|
|**Tie-In Station**|A point where a new pipeline connects (ties in) to an existing pipeline system. It often includes valves, fittings, and sometimes metering equipment.|Used for expansions, interconnections between operators, or connecting produced/recycled water lines.|**Point** (with line relationships to main and branch pipelines)|
|**Pigging Station**|A facility for launching or receiving “pigs” — mechanical devices that clean or inspect pipelines. Two main types: launcher (send) and receiver (receive).|Used for maintenance and flow assurance on longer water pipelines, especially with solids or biofouling.|**Point** (can be dual points if launcher/receiver are separate)|
|**Riser**|A vertical pipeline section connecting underground (buried) lines to aboveground facilities or manifolds.|Found at transitions between surface and subsurface flow, often at tanks, pits, or well pads.|**Point or short line** (used in both 2D and 3D mapping for vertical geometry)|

---

### 🗂️ Recommended GIS Feature-Class Structure

For **Enterprise Geodatabases (EGIS)**, these components are usually implemented as **feature classes** under a “Pipeline Facilities” dataset. Below are template attribute fields recommended for each:

#### **1. Tie-In Stations Feature Class**

|Field|Type|Example|
|---|---|---|
|FACILITY_ID|Text|TI-NM-001|
|NAME|Text|JesseJames-Raptor Tie-In|
|STATUS|Text (Domain)|Active / Planned / Inactive|
|CONNECTS_FROM|Text|Mainline_001|
|CONNECTS_TO|Text|Spur_004|
|OPERATOR|Text|Select Energy|
|INSTALL_DATE|Date|2024-05-07|
|INSPECTION_FREQ|Text|Annual|
|COORD_METHOD|Text|GPS / Surveyed|

#### **2. Pigging Stations Feature Class**

| Field        | Type          | Example                          |
| ------------ | ------------- | -------------------------------- |
| FACILITY_ID  | Text          | PG-TX-002                        |
| STATION_TYPE | Text (Domain) | Launcher / Receiver              |
| FLUID_TYPE   | Text          | Produced Water                   |
| OPERATOR     | Text          | Select Energy                    |
| DIAMETER     | Double        | 12                               |
| PIPE_ID      | Text          | PL-BSRS-01                       |
| LAST_PIG_RUN | Date          | 2025-04-15                       |
| CONDITION    | Text          | Good / Needs Service             |
| NOTES        | Text          | Receiver cleaned after backflush |

#### **3. Risers Feature Class**

|Field|Type|Example|
|---|---|---|
|FACILITY_ID|Text|RS-NM-010|
|Riser_Type|Text (Domain)|Inlet / Outlet / Vent|
|MATERIAL|Text|Steel / HDPE|
|DIAMETER|Double|8|
|ELEVATION_TOP|Double|4350.25|
|ELEVATION_BOTTOM|Double|4325.10|
|CONNECTED_PIPELINE|Text|PL-J3-07|
|STATUS|Text|Active|
|COORD_METHOD|Text|Survey-Grade GPS|

---

### 📐 Spatial Design Tips

- Use **subtypes** to categorize: `Tie-In`, `Pigging`, `Riser`.
    
- Include **relationship classes** to the main `PipelineCenterline` feature class.
    
- If modeling 3D (for risers), enable Z-values and store elevation attributes.
    
- Domains should enforce standardized values for `Status`, `Material`, and `Operator`.
    

---

### 📑 GIS Template Resources

You can model your schema using any of these public templates:### 🧭 Functional Differences

|Component|Description|Common Use in Midstream Water Systems|Typical GIS Feature Type|
|---|---|---|---|
|**Tie-In Station**|A point where a new pipeline connects (ties in) to an existing pipeline system. It often includes valves, fittings, and sometimes metering equipment.|Used for expansions, interconnections between operators, or connecting produced/recycled water lines.|**Point** (with line relationships to main and branch pipelines)|
|**Pigging Station**|A facility for launching or receiving “pigs” — mechanical devices that clean or inspect pipelines. Two main types: launcher (send) and receiver (receive).|Used for maintenance and flow assurance on longer water pipelines, especially with solids or biofouling.|**Point** (can be dual points if launcher/receiver are separate)|
|**Riser**|A vertical pipeline section connecting underground (buried) lines to aboveground facilities or manifolds.|Found at transitions between surface and subsurface flow, often at tanks, pits, or well pads.|**Point or short line** (used in both 2D and 3D mapping for vertical geometry)|

---

### 🗂️ Recommended GIS Feature-Class Structure

For **Enterprise Geodatabases (EGIS)**, these components are usually implemented as **feature classes** under a “Pipeline Facilities” dataset. Below are template attribute fields recommended for each:

#### **1. Tie-In Stations Feature Class**

|Field|Type|Example|
|---|---|---|
|FACILITY_ID|Text|TI-NM-001|
|NAME|Text|JesseJames-Raptor Tie-In|
|STATUS|Text (Domain)|Active / Planned / Inactive|
|CONNECTS_FROM|Text|Mainline_001|
|CONNECTS_TO|Text|Spur_004|
|OPERATOR|Text|Select Energy|
|INSTALL_DATE|Date|2024-05-07|
|INSPECTION_FREQ|Text|Annual|
|COORD_METHOD|Text|GPS / Surveyed|

#### **2. Pigging Stations Feature Class**

|Field|Type|Example|
|---|---|---|
|FACILITY_ID|Text|PG-TX-002|
|STATION_TYPE|Text (Domain)|Launcher / Receiver|
|SERVICE_TYPE|Text|Produced Water|
|OPERATOR|Text|Select Energy|
|DIAMETER|Double|12|
|PIPE_ID|Text|PL-BSRS-01|
|LAST_PIG_RUN|Date|2025-04-15|
|CONDITION|Text|Good / Needs Service|
|NOTES|Text|Receiver cleaned after backflush|

#### **3. Risers Feature Class**

|Field|Type|Example|
|---|---|---|
|FACILITY_ID|Text|RS-NM-010|
|Riser_Type|Text (Domain)|Inlet / Outlet / Vent|
|MATERIAL|Text|Steel / HDPE|
|DIAMETER|Double|8|
|ELEVATION_TOP|Double|4350.25|
|ELEVATION_BOTTOM|Double|4325.10|
|CONNECTED_PIPELINE|Text|PL-J3-07|
|STATUS|Text|Active|
|COORD_METHOD|Text|Survey-Grade GPS|

---

### 📐 Spatial Design Tips

- Use **subtypes** to categorize: `Tie-In`, `Pigging`, `Riser`.
    
- Include **relationship classes** to the main `PipelineCenterline` feature class.
    
- If modeling 3D (for risers), enable Z-values and store elevation attributes.
    
- Domains should enforce standardized values for `Status`, `Material`, and `Operator`.
    

---

## 💧 What a Booster Station Is

### **Definition**

A **booster station** is a **pump facility** placed along a water pipeline to **increase hydraulic pressure** and maintain sufficient flow rate and head over long distances or elevation changes.

In **midstream water systems**, it is used to:

- Maintain line pressure in long transfer pipelines (produced, fresh, or recycled water).
    
- Overcome elevation gain between pits, tanks, or disposal wells.
    
- Enable multiple pressure zones or push water from one district/segment to another.
    

---

## ⚙️ Role in the Pipeline Network

|Function|Description|Related Components|
|---|---|---|
|**Pressure Support**|Re-pressurizes water mid-line to sustain flow rate over distance.|Often located between two tie-in points or near risers to elevation gains.|
|**Flow Management**|Allows operations teams to control flow rates for different destinations.|Integrated with SCADA, meters, and telemetry sensors.|
|**System Redundancy**|Includes multiple pumps (primary/backup) to ensure uptime.|May tie into generator or VFD control systems.|

---

## 🗺️ GIS Context

In your **Enterprise Geodatabase (EGIS)**, a **booster station** should be modeled as a **point feature class**, similar to tie-ins or pigging stations, but with distinct operational attributes and relationships.

It typically connects **two pipeline segments**:

`[Upstream Pipeline] → [Booster Station] → [Downstream Pipeline]`

---

## 🧭 Recommended Feature Class Schema

**Feature Class Name:** `BoosterStations`

|Field|Type|Example|Description|
|---|---|---|---|
|FACILITY_ID|Text|BS-NM-001|Unique ID for the station|
|NAME|Text|LostTank-Booster01|Station or pump skid name|
|STATUS|Text (Domain)|Active / Standby / Inactive|Current operational state|
|PIPELINE_ID|Text|PL-JesseRaptor-01|Associated pipeline segment|
|PRESSURE_IN|Double|75|Inlet pressure (psi)|
|PRESSURE_OUT|Double|115|Outlet pressure (psi)|
|FLOW_CAPACITY_BPD|Double|65000|Design throughput (barrels/day)|
|POWER_SOURCE|Text (Domain)|Electric / Diesel / Natural Gas|Energy source for pump|
|NUM_PUMPS|Short Integer|2|Count of pumps installed|
|CONTROL_SYSTEM|Text|SCADA / Manual / Remote|Control/telemetry system|
|INSTALL_DATE|Date|2024-09-01|Commissioning date|
|LAST_MAINT_DATE|Date|2025-03-10|Last maintenance|
|OPERATOR|Text|Select Energy|Managing company|
|NOTES|Text|Redundant VFD pumps with backup generator|Freeform info|

---

## 🧩 Suggested GIS Integration

- **Dataset:** Add under your `PipelineFacilities` feature dataset.
    
- **Subtype:** Booster Station (alongside Tie-In, Pigging, Riser, etc.).
    
- **Relationships:**
    
    - `PipelineCenterlines` (via `Pipeline_ID`)
        
    - `SCADA_Points` (for pressure and flow sensors)
        
- **Domains:**
    
    - `STATUS` → {Active, Planned, Inactive, Standby}
        
    - `POWER_SOURCE` → {Electric, Diesel, Solar, Gas}