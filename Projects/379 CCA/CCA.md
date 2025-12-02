
![[Pasted image 20251010135455.png]]

All one Dedication - One polygon for everying inside this ROFT area outlined in blue
Section 13 NO section 15 Yes



0016 - Delete as it is essentially a dupe
0022 Delete as it also was part of the old contract

0020 - git rid of Blip
![[Pasted image 20251010140113.png]]



Enverus Operator Land Future

marathon = Conoco 

Vital = Crecent 
or vital 

Operator Land for Enverus API - Integers 

# OGR and QGIS oiperations for geomtry calc

|Goal|QGIS Tool|GDAL / OGR Command|Notes|
|---|---|---|---|
|**Buffer geometries**|_Vector Geometry → Buffer_|`ogr2ogr -dialect sqlite -sql "SELECT ST_Buffer(geometry, distance) AS geom, * FROM input"`|Works with meters if your CRS is projected.|
|**Union or merge polygons**|_Vector Overlay → Union / Dissolve_|`ogr2ogr -dialect sqlite -sql "SELECT ST_Union(geometry) FROM input"`|Dissolve by attribute with `GROUP BY fieldname`.|
|**Intersect / Clip**|_Vector Overlay → Intersection / Clip_|`ogr2ogr -dialect sqlite -sql "SELECT ST_Intersection(a.geometry,b.geometry) FROM a,b"`|Be sure both layers share CRS.|
|**Calculate centroids**|_Vector Geometry → Centroids_|`ogr2ogr -dialect sqlite -sql "SELECT ST_Centroid(geometry) AS geom, * FROM input"`|Produces point layer.|
|**Measure or calculate area / length**|Field Calculator → `$area` / `$length`|`ogrinfo -dialect sqlite -sql "SELECT ST_Area(geometry), ST_Length(geometry) FROM input"`|Units depend on CRS.|
|**Simplify geometries**|_Vector Geometry → Simplify_|`ogr2ogr -simplify 10 output.shp input.shp`|Simplify tolerance in map units.|
|**Reproject data**|_Reproject Layer_|`ogr2ogr -t_srs EPSG:#### out.shp in.shp`|Set EPSG code to desired coordinate system.|
|**Fix invalid geometries**|_Vector Geometry → Fix Geometries_|`ogr2ogr -dialect sqlite -sql "SELECT ST_MakeValid(geometry) FROM input"`|Useful for polygons that fail|