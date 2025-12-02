

# Select Boundary Polygons

# Pit Camera Point
|Field Name|Field Name Alias|Field Description|Field Type|Length|Domain (Drop down)|Field Order|Comments|Existing or New Field|
|---|---|---|---|---|---|---|---|---|
|pitcamera_id|Pit Camera ID|Primary business key for the pit camera (unique within organization).|Text|255|No|1||New|
|asset_name|Asset Name|Common or facility name.|Text|255|No|2||New|
|asset_type|Asset Type|Category of asset; from domain.|Text|255|Yes-see domains tab|3|Domain: Asset Type|New|
|status|Status|Operational status from domain.|Text|50|Yes-see domains tab|4|Domain: Status|New|
|sws_region|SWS Region|Select geographic region.|Text|50|Yes-see domains tab|5|Domain: SWS Region|New|
|state|State|Two-letter state abbreviation.|Text|2|Yes-see domains tab|6|Domain: State|New|
|county|County|County name.|Text|50|Yes-see domains tab|7|Domain: County|New|
|address|Address|Street or site address.|Text|50|No|8||New|
|city|City|City name.|Text|50|No|9||New|
|zipcode|Zip Code|Postal zip code.|Text|10|No|10||New|
|gis_lat|GIS Lat|Latitude of point (decimal degrees).|Double|11,8|No|11||New|
|gis_long|GIS Long|Longitude of point (decimal degrees).|Double|12,8|No|12||New|
|spatial_accuracy|Spatial Accuracy|Positional accuracy class from domain.|Text|50|Yes-see domains tab|13|Domain: Spatial Accuracy|New|
|imagery_source|Imagery Source|Source of aerial/satellite imagery used to digitize pit (e.g., Google Earth, ArcGIS Imagery, DroneDeploy).|Text|100|Yes-see domains tab|14|Domain: Imagery Source|**New**|
|imagery_date|Imagery Date|Capture date of imagery used for digitizing.|Date – Date only|N/A|No|15|Supports validation of pit polygon creation|**New**|
|coordinate_source|Coordinate Source|Source of lat/longs (survey, GPS, API, manual digitizing).|Text|100|Yes-see domains tab|16|Domain: Coordinate Source|**New**|
|coord_validation|Coordinate Validation|Flag or result of coordinate QA (OK, Needs Review, Invalid).|Text|50|Yes-see domains tab|17|Domain: Coordinate Validation|**New**|
|related_asset|Related Asset|Pipeline, pond, or facility the camera is linked to.|Text|255|No|18|Used for linking to Enverus schema|**New**|
|distance_to_asset_ft|Distance to Asset (ft)|Linear distance from camera to related pit/facility centroid.|Double|N/A|No|19|Used for initial distance estimates|**New**|
|under_construction|Under Construction|Indicates if associated pit/facility is still under construction.|Text|10|Yes-see domains tab|20|Domain: Yes/No|**New**|
|dd_url|Drone Deploy URL|URL link to DroneDeploy photo or inspection data.|Text|500|No|21||New|
|driving_directions|Driving Directions|Access or navigation instructions to reach site.|Text|500|No|22||New|
|legacy_business|Legacy Business|Historical business division; from domain.|Text|255|Yes-see domains tab|23|Domain: Legacy Business|New|
|sd_request_id|SD Request ID|Related Service Desk request or ticket number.|Long|N/A|No|24||New|
|notes|Notes|General comments or remarks.|Text|500|No|25|Include data quality issues or missing imagery notes|New|
|camera_heading_deg|Camera Heading (deg)|Camera azimuth direction (degrees from north).|Double|N/A|No|26|Optional field for orientation|New|
|camera_tilt_deg|Camera Tilt (deg)|Downward or upward camera angle relative to horizontal.|Double|N/A|No|27|Optional field for orientation|New|
|mount_height_ft|Mount Height (ft)|Height of camera above ground level (feet).|Double|N/A|No|28|Optional field for setup details|New|
|capture_schedule|Capture Schedule|Frequency or timing of photo captures.|Text|100|No|29|Optional operational detail|New|
|power_source|Power Source|Power supply type (solar, AC, battery, etc.).|Text|50|No|30|Optional operational detail|New|
|connectivity|Connectivity|Data connectivity method (cellular, Wi-Fi, satellite).|Text|50|No|31|Optional operational detail|New|
|last_inspect_date|Last Inspect Date|Date of last camera inspection or maintenance.|Date – Date only|N/A|No|32|Optional maintenance tracking|New|
|data_load_source|Data Load Source|Origin of data load (Amir notebook, manual entry, API import).|Text|100|Yes-see domains tab|33|Domain: Data Source|**New**|
|validation_status|Validation Status|GIS validation state (Pending, Passed, Failed).|Text|50|Yes-see domains tab|34|Domain: Validation Status|**New**|
|water_rights_link|Water Rights Link|Related water rights or parcel record identifier (future use).|Text|255|No|35|Reserved for future parcel integration|**New**|
|created_user|Creator|Username of feature creator.|Text|255|No|36|System field (Editor Tracking)|Existing|
|created_date|Created|Date created.|Date – Date only|N/A|No|37|System field (Editor Tracking)|Existing|
|last_edited_user|Editor|Username of last editor.|Text|255|No|38|System field (Editor Tracking)|Existing|
|last_edited_date|Edited|Date last edited.|Date – Date only|N/A|No|39|System field (Editor Tracking)|Existing|
|globalid|Global ID|System-managed unique identifier.|GUID|N/A|No|40|System field (Enterprise)|Existing|
|shape|Shape|Geometry point for location.|Geometry (Point)|N/A|No|41|System field|Existing|

# North Cowden

Nathan
While reviewing the Pit features for Amir near the Jerrah Pipeline area, I noticed a few inconsistencies in pond naming that Amir had Questions about while doing the analysis for Pit camera placement.

Specifically:

- The Pit labeled **“North Cowden–Jerrah 1”** appears to be named for its destination possibly rather than its associated asset as it is in NM and N Cowden is in TX . It is located North of Lost Tank TRE and North of Jerrah-Pond 1 which too is not at the TRE Jerrah Pond 3 which does have 2 pits "Jerrah-Pond 3" and Jerrah-Pond 2".
    ![[Pasted image 20251022104812.png]]
Could you confirm:

1. Whether **“North Cowden–Jerrah 1”** is owned or operated by us?
    
2. What the official or preferred naming convention should be for the **Jerrah** series of ponds?
    

An existing infrastructure KMZ from a trusted source doesn’t show “North Cowden–Jerrah 1,” so I want to make sure we’re aligned before updating the schema or map layer


# Referring to collections of Assests
 The storage or assets at "Site" 
 Systems contain subsystems,
 subsystems contain sites, 
 sites contain various types of assets such as storages 


# Todo 

###### **Google Earth Images to Double check**   
- [ ] URR East Pond Location   
- [x] N. Cowden Jerrah 1  
- [ ] Nothcuttt

###### **ID for Point Feature Class that ties it to the Polygon and the Asset**   
all storage ID  - Dataverse ID
**Dataverse ID for each FC**  
Polygon Feature Class needs to be added to   
Madison - Attribute Rules - Documentation on which ones need to be filled out / Turned on

###### **Unique ID for Facility_storage_dataverse**
connects Facility (TRE) to Storage and now will connect storage and site to camera and Pit boundary

###### **Drop Down for Status**  
Camera-location_status  
Camera  
installed /   
proposed

###### **Provide Schema to Nate Bonda - SD**




## 🛰️ **Top Free Imagery Viewers by Lat/Long (non-Google)**

| Viewer                                                    | Imagery Type                                            | Typical Resolution             | Strengths                                                                 | Link                                                                                |
| --------------------------------------------------------- | ------------------------------------------------------- | ------------------------------ | ------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| **USGS EarthExplorer**                                    | Landsat, Sentinel-2, NAIP, high-res public orthoimagery | 10 m–0.6 m (varies)            | Scientific datasets, free download, very complete metadata                | 🌎 https://earthexplorer.usgs.gov                                                   |
| **ESA Sentinel Hub EO Browser**                           | Sentinel-1 (SAR), Sentinel-2 (optical), Landsat         | 10–30 m                        | Cloudless composites, multiple spectral bands (NDVI, etc.), live timeline | 🌍 https://eobrowser.org                                                            |
| **USDA NAIP Image Server (through ArcGIS or web viewer)** | National Agriculture Imagery Program                    | ~0.6 m                         | Sharp, true-color aerials for all U.S. states (refreshed every 2–3 yrs)   | 🗺️ https://datagateway.nrcs.usda.gov                                               |
| **ESRI World Imagery (ArcGIS Online)**                    | Esri/Maxar curated base imagery                         | 0.3–1 m (many areas)           | Great base map in ArcGIS Online, updates quarterly                        | 🌐 https://www.arcgis.com/home/webmap/viewer.html → Add _Imagery (Clarity)_ basemap |
| **Zoom Earth**                                            | NASA/NOAA + Maxar composites                            | ~5 m global; up to 0.5 m urban | Very fast, modern interface, real-time weather layers                     | 🌎 https://zoom.earth                                                               |
| **Bing Maps Aerial**                                      | Commercial aerial (Microsoft/Maxar)                     | ~0.3 m urban                   | Good clarity, viewable in ArcGIS or web                                   | 🌐 https://www.bing.com/maps                                                        |
| **NASA Worldview**                                        | MODIS, VIIRS (daily)                                    | 250 m–1 km                     | Excellent for environmental change / fire / smoke tracking                | 🛰️ https://worldview.earthdata.nasa.gov                                            |
| **MapTiler Satellite / OpenAerialMap**                    | Open-source imagery                                     | 0.5–2 m                        | Free, open-data ethos, API available                                      | 🌍 https://openaerialmap.org                                                        |

---

## 🧭 For U.S. Midstream / Oilfield Use (Your Best Bet)

Because your work is in **New Mexico** and **Texas**, the **best practical imagery** sources are:

### 🔹 **USDA NAIP**

- **Resolution:** 0.6 m (2 ft).
    
- **Color:** True color and CIR.
    
- **Access:**
    
    - ArcGIS Pro: add the _“NAIP Imagery”_ layer from Living Atlas.
        
    - Or view/download via USGS EarthExplorer → NAIP dataset.
        
- **Why it’s great:** perfect for pit outlines, pipelines, yards — detailed enough for infrastructure QA.
    

### 🔹 **Esri World Imagery (Clarity)**

- Free to view in any browser at ArcGIS Online Map Viewer.
    
- Often newer than Google Earth in rural Permian areas (uses Maxar & Esri updates).
    
- Accepts direct **lat/long search** (just type `31.5981, -102.8724`).
    

### 🔹 **Bing Aerial**

- Clear and bright, similar resolution to Google.
    
- Often newer around Midland/Odessa or Lea/Eddy counties.
    
- Use bing.com/maps, paste your coordinates, and switch to **Aerial**.
    

---

## 🧩 Quick Lat/Long Example

Say you want to check a pit at **31.5981 N, –102.8724 W**:

|Platform|How to use it|
|---|---|
|**Esri World Imagery**|Go to ArcGIS Map Viewer → search box → `31.5981 -102.8724`|
|**Zoom Earth**|https://zoom.earth/#31.5981,-102.8724,17z|
|**EarthExplorer**|Search Coordinates → choose datasets → NAIP → “Show Results”|

---

## ⚙️ Bonus: Pull into ArcGIS Pro

You can add these imagery services directly:

- **NAIP:** `https://gis.apfo.usda.gov/arcgis/rest/services/NAIP/ImageServer`
    
- **Esri World Imagery:** built-in basemap
    
- **Bing Maps Aerial:** available if your organization has a Bing key
    

Then you can measure, digitize, and export directly without using Google Earth.