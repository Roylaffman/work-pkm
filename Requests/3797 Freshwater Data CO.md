
## 1. What CDSS / HydroBase tells us

- CDSS’s **HydroBase** (Colorado) stores **diversion structure records**, which include not only the physical structure (headgate, canal, reservoir) but also time series of diversion volumes. [opencdss.state.co.us](https://opencdss.state.co.us/tstool/13.00.00/doc-user/datastore-ref/CO-HydroBase/CO-HydroBase/?utm_source=chatgpt.com)
    
- Key attributes in CDSS:
    
    - **WDID**: Water-diversion identification number (8 digits)
        
    - **Diversion Class** (DivClass) — categorizes diversion type or usage
        
    - **DWR** (likely referring to Division of Water Resources)
        
    - **DivComment**: comments (e.g. “not used” flags)
        
    - Time‐series storage: daily, monthly, annual diversion totals. In HydroBase:
        
        - Daily data are carried forward within the “irrigation year” (Nov–Oct) when at least one observation is present. [opencdss.state.co.us](https://opencdss.state.co.us/tstool/13.00.00/doc-user/datastore-ref/CO-HydroBase/CO-HydroBase/?utm_source=chatgpt.com)
            
        - Monthly and annual values derive from daily totals. [opencdss.state.co.us](https://opencdss.state.co.us/tstool/13.00.00/doc-user/datastore-ref/CO-HydroBase/CO-HydroBase/?utm_source=chatgpt.com)
            
- Therefore, for diversions/headgates in Colorado, you’ll often link to the HydroBase via **WDID**, and possibly include diversion class, comment flags, and time‐series summaries (daily, monthly, annual).
    

---

## 2. What the USGS Monitoring Locations / Water Data API provides

The USGS OGC API for monitoring locations is a standardized way to fetch metadata about monitoring points (gauges, wells, canals, etc.). Some key schema elements from their “Monitoring locations” endpoint: [api.waterdata.usgs.gov](https://api.waterdata.usgs.gov/ogcapi/v0/collections/monitoring-locations/schema?f=html&utm_source=chatgpt.com)

**Core fields in USGS monitoring locations:**

| Field                                                    | Description                                                         |
| -------------------------------------------------------- | ------------------------------------------------------------------- |
| `id`                                                     | Unique monitoring location ID (agency + monitoring_location_number) |
| `agency_code`                                            | The agency code (e.g. “USGS”)                                       |
| `monitoring_location_number`                             | The station number (8- to 15-digit string)                          |
| `monitoring_location_name`                               | Name of the site (e.g. “Canal 4 at Headgate”)                       |
| `site_type_code` / `site_type`                           | Hydrologic type (stream, canal, well, etc.)                         |
| `geometry`                                               | Geographic location (point)                                         |
| `state_code`, `county_code`                              | Location’s political subdivisions                                   |
| `hydrologic_unit_code` (HUC)                             | Watershed/drainage area code                                        |
| `altitude`, `altitude_accuracy`, `altitude_method_code`  | Elevation information                                               |
| `horizontal_position_method_code`, `horizontal_accuracy` | How coordinates were determined / accuracy                          |
| `vertical_datum`                                         | The vertical reference (e.g. NAVD88)                                |

Because USGS uses the OGC API / Features interface, the monitoring locations are accessible as GeoJSON-like features. [api.waterdata.usgs.gov+2api.waterdata.usgs.gov+2](https://api.waterdata.usgs.gov/ogcapi/v0/collections/monitoring-locations?utm_source=chatgpt.com)

For example, the site **“CANAL 4 AT HEADGATE”** (site number 384840104481200) is documented by USGS as a canal/ headgate monitoring point: [USGS Water Data](https://waterdata.usgs.gov/monitoring-location/384840104481200/?utm_source=chatgpt.com)

---

## 3. What is a “Diversion Point / Headgate / Canal Monitoring Location”

In your domain, such a point is where water is **diverted** off a river or stream into a canal, ditch, or irrigation system. It often includes a structure (gate, weir, sluice) that regulates flow, and it might be instrumented with sensors (gage, flow meter, staff gauge). In GIS and hydrologic modeling:

- It is often referred to as a **diversion structure**, **headgate**, **diversion point**, or **intake**.
    
- It may be co-located with USGS monitoring stations (if the diversion is measured) or separately maintained by state agencies (CDSS in Colorado).
    
- It may have **dual identifiers**: one identifier in the state diversion database (WDID, state ID), and another in USGS (monitoring location number).
    

You want a feature class that supports **both** types of identifiers and associated metadata, so that when you join or merge data you can link appropriately.

---

## 4. Designing Your Feature Class for Diversions / Headgates

Based on what CDSS and USGS provide, here’s a suggested **feature class schema** (point features) and workflow considerations:

### Suggested Fields / Attributes

|Field Name|Type|Description / Notes|Source(s)|
|---|---|---|---|
|DiversionID|Text / String|Your internal primary key|–|
|WDID|Text (8 chars)|Colorado diversion ID (if available)|CDSS|
|USGS_MonLoc_Num|Text|USGS monitoring_location_number|USGS|
|USGS_MonLoc_ID|Text|Full USGS `id` (e.g. “USGS-12345678”)|USGS|
|Name|Text|Name of the diversion / headgate|Both|
|Site_Type|Text|e.g. “Canal / Headgate / Ditch / Intake”|USGS `site_type` or CDSS class|
|State|Text|State (e.g. “CO”)|USGS / your jurisdiction|
|County|Text|County name or code|USGS|
|HUC|Text|Hydrologic unit (watershed) code|USGS / derived|
|Elevation|Double / Float|Altitude (e.g. feet or meters)|USGS or local survey|
|Elev_Accuracy|Double|Accuracy or error margin|USGS|
|Horizontal_Accuracy|Text / Double|e.g. “±1 m”|USGS|
|Datum_Vertical|Text|Vertical datum (e.g. NAVD88)|USGS|
|Coord_Method|Text|How position was determined (GPS, interpolation, survey)|USGS / state data|
|Year_Installed|Date / Year|When diversion structure built / surveyed|State data|
|Status|Text|Active / Inactive / Abandoned|State data|
|Structure_Type|Text|Types: gate, weir, headgate, diversion dam|Domain|
|Comments|Text|Misc notes|–|
|TimeSeries_Link|Text / URL|Link to time series resource in USGS or CDSS|–|

You can also include **subtype** or **category** domains to distinguish whether a feature is strictly a diversion (CDSS only) or a monitored USGS location, or both.

### Key Relationships & Joins

- **One-to-one or one-to-many**: A diversion might have multiple time series (daily, monthly) in CDSS or multiple parameters in USGS.
    
- **Join key**: Use WDID to join to CDSS diversion time series and USGS_MonLoc_ID / number to join to USGS time series.
    
- **Nulls**: Some diversions will not be in USGS; some USGS monitoring sites will not correspond to a diversion structure. The schema must support missing identifiers.
    

### CRS, Geometry, and Metadata

- Use a **geographic CRS** (e.g. WGS84 / EPSG:4326) for USGS compatibility.
    
- Capture **coordinate method, horizontal accuracy, datum** fields from USGS schema. [api.waterdata.usgs.gov](https://api.waterdata.usgs.gov/ogcapi/v0/collections/monitoring-locations/schema?f=html&utm_source=chatgpt.com)
    
- Ensure **time zone metadata** (USGS provides `time_zone_abbreviation` and `uses_daylight_savings`). [api.waterdata.usgs.gov](https://api.waterdata.usgs.gov/ogcapi/v0/collections/monitoring-locations/schema?f=html&utm_source=chatgpt.com)
    

---

## 5. Workflow & API Integration Considerations

- Use the **USGS Monitoring Locations API** to fetch metadata (geometry + attributes) via `/monitoring-locations` endpoint (returns JSON features). [api.waterdata.usgs.gov+2api.waterdata.usgs.gov+2](https://api.waterdata.usgs.gov/ogcapi/v0/collections/monitoring-locations?utm_source=chatgpt.com)
    
- Use the CDSS HydroBase (via TSTool or state APIs) to get diversion volume time series, comments, and WDID‐based metadata. [opencdss.state.co.us](https://opencdss.state.co.us/tstool/13.00.00/doc-user/datastore-ref/CO-HydroBase/CO-HydroBase/?utm_source=chatgpt.com)
    
- You’ll likely fetch USGS metadata first, import into GIS, then augment with CDSS data by matching on location coordinates and WDID mapping (if available).
    
- When fetching time series / discharge or diversion volumes from USGS or CDSS, store them in a separate **time series table** or **relate table**, not directly in the point feature class (to avoid heavy data in attributes).


# List of Counties for Domain
|             |
| ----------- |
| Adams       |
| Alamosa     |
| Arapahoe    |
| Archuleta   |
| Baca        |
| Bent        |
| Boulder     |
| Broomfield  |
| Chaffee     |
| Cheyenne    |
| Clear Creek |
| Conejos     |
| Costilla    |
| Crowley     |
| Custer      |
| Delta       |
| Denver      |
| Dolores     |
| Douglas     |
| Eagle       |
| Elbert      |
| El Paso     |
| Fremont     |
| Garfield    |
| Gilpin      |
| Grand       |
| Gunnison    |
| Hinsdale    |
| Huerfano    |
| Jackson     |
| Jefferson   |
| Kiowa       |
| Kit Carson  |
| La Plata    |
| Lake        |
| Larimer     |
| Las Animas  |
| Lincoln     |
| Logan       |
| Mesa        |
| Mineral     |
| Moffat      |
| Montezuma   |
| Montrose    |
| Morgan      |
| Otero       |
| Ouray       |
| Park        |
| Phillips    |
| Pitkin      |
| Prowers     |
| Pueblo      |
| Rio Blanco  |
| Rio Grande  |
| Routt       |
| Saguache    |
| San Juan    |
| San Miguel  |
| Sedgwick    |
| Summit      |
| Teller      |
| Washington  |
| Weld        |
| Yuma        |