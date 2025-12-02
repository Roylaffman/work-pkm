
The Enverus API gives points and lines an SRID of 4326 we use 3875 

![[EnverusAPI SRID 4326.png]]
In PostGIS and other geospatial contexts, 4326 and 3857 are Spatial Reference System Identifiers (SRIDs) that represent specific coordinate reference systems (CRSs).

- **EPSG:4326 (WGS 84 Geographic Coordinate System)**:
- This is what the ENverus API Uses
    - This is a geographic coordinate system (GCS), meaning it uses latitude and longitude to define locations on a spherical or ellipsoidal model of the Earth (specifically, the WGS84 ellipsoid).
    - Units are in degrees.
    - It's commonly used for storing and referencing global data, especially with GPS data, and is the default for PostGIS and GeoJSON.
    
- **EPSG:3857 (WGS 84 / Pseudo-Mercator or Web Mercator)**:
    - This is what we uses at Select!
    - This is a projected coordinate system (PCS), meaning it projects the WGS84 geographic coordinates onto a flat, two-dimensional surface using the Mercator projection.
    - Units are in meters.
    - It's widely used in web mapping applications like Google Maps, OpenStreetMap, and many web mapping libraries, as it allows for easy tile generation and consistent display across different zoom levels.
    - While convenient for display, it introduces distortion in area and shape, especially at higher latitudes, and is generally not recommended for precise measurements or calculations.



### **1. Enable PostGIS Extension**

First, ensure PostGIS is installed and enabled in your database:

##### Sql


`CREATE EXTENSION postgis;`

### **2. Transform Geometry to Web Mercator (EPSG:3857)**

You can use the `ST_Transform` function to convert geometries to the Web Mercator projection:

##### Sql

Copy code

`SELECT ST_Transform(geom, 3857) AS web_mercator_geom FROM your_table;`

- `geom`: The geometry column in your table.
- `3857`: The SRID for Web Mercator.

### **3. Example: Insert Point in Web Mercator**

If you want to insert a point in Web Mercator:

##### Sql


`INSERT INTO your_table (geom) VALUES (ST_Transform(ST_SetSRID(ST_MakePoint(-82.5515, 35.5951), 4326), 3857));`

- `ST_MakePoint`: Creates a point with longitude and latitude.
- `ST_SetSRID`: Assigns the SRID (4326 for WGS84).
- `ST_Transform`: Converts the point to Web Mercator (EPSG:3857).

### **4. Querying Data in Web Mercator**

To retrieve data in Web Mercator:

##### Sql



`SELECT ST_AsText(ST_Transform(geom, 3857)) AS web_mercator_geom FROM your_table;`