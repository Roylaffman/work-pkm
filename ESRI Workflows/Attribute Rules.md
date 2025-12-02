


# How to 

Madison Link:


# Ideas
### 1) `GIS_Acres` — Calculation (Insert, Update)

- **Purpose:** Always keep acres in sync with geometry.
    
- **Rule:** Calculation
    
- **Trigger:** Insert, Update
    
- **Arcade:**
    

`// Geodesic area in m² → acres var a_m2 = AreaGeodetic($feature, 'square-meters'); return Round(a_m2 / 4046.8564224, 4);`

### 2) `GIS_SQ_Miles` — Calculation (Insert, Update)

- **Purpose:** Always keep square miles in sync with geometry.
    
- **Rule:** Calculation
    
- **Trigger:** Insert, Update
    
- **Arcade:**
    

`// Geodesic area in m² → square miles var a_m2 = AreaGeodetic($feature, 'square-meters'); return Round(a_m2 / 2589988.110336, 6);`

> Tip: Use **Exempt from application evaluation** unchecked so edits in Pro/Portal will evaluate the rules. Keep both on Insert and Update so geometry edits recalc.

---

# Strongly recommended (quality/consistency)

### 3) `boundary_id` — Constraint: unique & not empty

- **Purpose:** Prevent duplicate IDs.
    
- **Rule:** Constraint
    
- **Trigger:** Insert, Update
    
- **Arcade:**
    

`// Enforce unique non-null boundary_id if (IsEmpty($feature.boundary_id)) { return false; }  var fs = FeatureSetByName($datastore, $layer, ['boundary_id'], false); var dupes = Count(Filter(fs, "boundary_id = @boundary_id AND OBJECTID <> @OBJECTID")); return dupes == 0;`

- **Error number/message (example):** 1001 / "boundary_id must be unique and not empty."
    

> Optional: If your RDBMS supports sequences, add a **Calculation** rule that fills `boundary_id` when blank:

`// Only set if empty; requires a database sequence named seq_boundary_id IIf(IsEmpty($feature.boundary_id), NextSequenceValue('seq_boundary_id'), $feature.boundary_id)`

### 4) `state` — Constraint: 2-letter code (uppercase)

- **Purpose:** Enforce standard state codes alongside the domain.
    
- **Rule:** Constraint
    
- **Trigger:** Insert, Update
    
- **Arcade:**
    

`var s = $feature.state; if (IsEmpty(s)) { return false; } var ok = Regex_IsMatch(Upper(s), "^[A-Z]{2}$"); return ok;`

- **Error:** 1002 / "State must be a 2-letter code."
    

### 5) `zipcode` — Constraint: 5-digit or ZIP+4

- **Purpose:** Keep zips valid.
    
- **Rule:** Constraint
    
- **Trigger:** Insert, Update
    
- **Arcade:**
    

`var z = Trim(Text($feature.zipcode)); if (IsEmpty(z)) { return true; } // allow blank if not required return Regex_IsMatch(z, "^[0-9]{5}(-[0-9]{4})?$");`

- **Error:** 1003 / "Zip Code must be 12345 or 12345-6789."
    

### 6) `dd_url` — Validation: looks like a URL

- **Purpose:** Catch obvious typos; doesn’t block the edit.
    
- **Rule:** Validation
    
- **Trigger:** Batch, On Change
    
- **Arcade:**
    

`var u = Trim(DefaultValue($feature.dd_url, "")); if (IsEmpty(u)) { return true; } return StartsWith(Lower(u), "https://") || StartsWith(Lower(u), "http://");`

- **Error (if using Constraint instead):** 1004 / "URL must start with http:// or https://"
    

### 7) `county` — Calculation lookup (optional but handy)

- **Purpose:** Auto-populate from a reference county layer in the same datastore.
    
- **Prereq:** A counties polygon layer named `Counties` with a `NAME` field.
    
- **Rule:** Calculation
    
- **Trigger:** Insert, Update
    
- **Arcade:**
    

`var counties = FeatureSetByName($datastore, "Counties", ["NAME","SHAPE"], true); var hit = First(Intersects(counties, $feature)); return IIf(IsEmpty(hit), $feature.county, hit.NAME);`

### 8) `sws_region` — Calculation lookup (if you maintain a regions layer)

- **Rule:** Calculation
    
- **Trigger:** Insert, Update
    
- **Arcade:**
    

`var regions = FeatureSetByName($datastore, "SWS_Regions", ["Region","SHAPE"], true); var hit = First(Intersects(regions, $feature)); return IIf(IsEmpty(hit), $feature.sws_region, hit.Region);`

### 9) `sd_request_id` — Constraint: non-negative integer

- **Rule:** Constraint
    
- **Trigger:** Insert, Update
    
- **Arcade:**
    

`var v = $feature.sd_request_id; return (IsEmpty(v) || v >= 0);`