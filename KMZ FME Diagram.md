---
tags:
  - fme
  - kmz
---
 
 [[KMZ data Structure]]
 
 
 ┌─────────────────────┐
 │     KML Reader      │
 │  (Expose desc/raw)  │
 └──────────┬──────────┘
            │
            ▼
 ┌─────────────────────┐
 │   GeometryFilter    │
 │ Points / Lines / Polys
 └──────────┬──────────┘
            │
            ▼
 ┌─────────────────────┐
 │    HTMLExtractor    │
 │  kml_description →  │
 │        NOTES        │
 └──────────┬──────────┘
            │
            ▼
 ┌─────────────────────┐
 │  AttributeManager   │
 │ Rename, clean, drop │
 │   Add NAME / NOTES  │
 └──────────┬──────────┘
            │
            ▼
 ┌─────────────────────┐
 │     Reprojector     │
 │     → EPSG:3857     │
 └──────────┬──────────┘
            │
            ▼
 ┌─────────────────────┐
 │  FGDB Writer (gdb)  │
 │  Points / Lines FCs │
 └─────────────────────┘


