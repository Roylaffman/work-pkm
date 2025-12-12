---
tags:
  - project
---



You have three “views” of essentially the same conceptual data:

1. **SQL sheet** – likely the canonical/geodatabase schema (internal).
2. **EXPORT sheet** – the schema actually being used in an export or API-oriented view.
3. **Developer API sample record** – how a single record is exposed in the developer API.

Below is a structured comparison.

---

### 1. How `SQL` vs `EXPORT` differ

Here are the heads of each sheet so you can see what we’re talking about:

- **SQL sheet sample:**

Export

![Google Sheets](https://julius.ai/_next/image?url=%2Fgsheets_logo.png&w=32&q=80)Export to Sheets

Expand

|Name|Alias|Type|Usage|Nullable|Domain|Default Value|Precision|Scale|Length|Editable|Required|Geodatabase Order|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|deleteddate|Deleteddate|Date||True|||0|0|0|True|False|500|
|dsuid|DSUID|String||True|||0|0|50|True|False|100|
|envbasin|Basin|String||True|||0|0|50|True|False|600|
|envcounty|County|String||True|||0|0|50|True|False|600|
|envinterval|Interval|String||True|||0|0|50|True|False|600|

- **EXPORT sheet sample:**

Export

![Google Sheets](https://julius.ai/_next/image?url=%2Fgsheets_logo.png&w=32&q=80)Export to Sheets

Expand

|Name|Alias|Type|Usage|Nullable|Domain|Default Value|Precision|Scale|Length|Editable|Required|Geodatabase Order|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|deleteddate|Deleted Date|Date|Index|True|||1|0|8|1.0|0.0|23.0|
|dsuid|DSU ID|String||True|||0|0|16|1.0|0.0|21.0|
|envbasin|Basin|String||True|||0|0|32|1.0|0.0|3.0|
|envcounty|County|String||True|||0|0|32|1.0|0.0|4.0|
|envinterval|Interval|String||True|||0|0|128|1.0|0.0|5.0|

Both sheets have the same _columns describing fields_:

- `Name`, `Alias`, `Type`, `Usage`, `Nullable`, `Domain`, `Default Value`, `Precision`, `Scale`, `Length`, `Editable`, `Required`, `Geodatabase Order`

The differences are in the **values** for each field definition.

#### a. `Name` column

- In both `SQL` and `EXPORT`, the underlying field `Name` values are the same (e.g. `deleteddate`, `dsuid`, `envbasin`, `envcounty`, `envinterval`).
- These map directly to the logical field names used in the underlying database.

#### b. `Alias` column

- **SQL** `Alias` tends to be “internal” style or minimally formatted:
    - e.g. `Deleteddate`, `DSUID`, `Basin`, `County`, `Interval`
- **EXPORT** `Alias` tends to be more user‑friendly / presentation‑oriented:
    - e.g. `Deleted Date`, `DSU ID`, `Basin`, `County`, `Interval`

So EXPORT is closer to **human‑readable labels**, while SQL aliases are closer to minimal/internal naming.

#### c. `Type` column

- For the sample rows, `Type` matches between `SQL` and `EXPORT`:
    - `deleteddate` is `Date` in both.
    - `dsuid`, `envbasin`, `envcounty`, `envinterval` are all `String`.
- This indicates strong consistency in data types between the two schemas.

#### d. `Usage` column

- **SQL**: `Usage` is often `None` (blank) for these rows.
- **EXPORT**: `Usage` may be populated for some fields:
    - `deleteddate`: `Index` (indicating it is used as an index in the export)
    - others still `None`.

EXPORT is adding **semantic roles** (like “Index”) that are not expressed in SQL.

#### e. `Precision`, `Scale`, `Length`

- Numerically, these fields differ between SQL and EXPORT.
    - Example: `deleteddate` precision is `0` in SQL vs `1` in EXPORT; length is `0` vs `8`.
    - String lengths are more constrained in EXPORT (e.g. `envbasin` length 50 in SQL vs 32 in EXPORT).
- Interpretation:
    - SQL looks like a _loosely specified geodatabase design_.
    - EXPORT tightens constraints for actual exported data (e.g., defining string max length, index precision).

#### f. `Editable`, `Required`, `Geodatabase Order`

- `Editable` and `Required` are stored as booleans/ints; conceptually:
    - EXPORT appears more opinionated on which fields are editable and where they appear (geodatabase order numbers are different: 500 vs 23, 100 vs 21, etc.).
    - The "Geodatabase Order" in EXPORT reflects the desired column order in an export / feature class, not necessarily the original SQL ordering.

**Summary:**

- **Same fields, different presentation and constraints.**
- SQL = internal design / GIS geodatabase definition.
- EXPORT = cleaned up for _external consumption_ (better alias labels, usage flags, lengths, and order).

---

### 2. How both compare to the Developer API sample record

Your sample API record is:

 Download Copy

```json
{
  "DSUID": "SS-1133",
  "DeletedDate": "2021-02-05T11:19:48Z",
  "ENVBasin": "ANADARKO",
  "ENVCounty": "CANADIAN",
  "ENVInterval": "CANEY | MERAMEC | UPPER SYCAMORE | LOWER SYCAMORE | OSAGE | WOODFORD",
  "ENVPWOperator": "COTERRA ENERGY",
  "ENVPWSpacingScenario": 1056,
  "ENVPeerGroup": "NON-PE-BACKED PRIVATE",
  "ENVPlay": "SCOOP|STACK",
  "ENVRegion": "MID-CONTINENT",
  "ENVSubPlay": "STACK LIQ RICH GAS",
  "ENVWellStatus": "INACTIVE PRODUCER",
  "Geom_WKT": "LINESTRING (-98.277265 35.696875, -98.277127 35.723621)",
  "LateralLength": 10023,
  "PWAPIUWI": "35-017-24764-00",
  "PWAzimuthFromGridNorth": 0.24056118485771094,
  "PWCoordinateSource": "ACTUAL",
  "PWLatitude": 35.695726,
  "PWLongitude": -98.278966,
  "PredictedOperatorSource": "Most Horizontal Length (Producing Wells)",
  "StateProvince": "OK",
  "Trajectory": "HORIZONTAL",
  "UpdatedDate": "2021-02-03T09:28:04Z",
  "WellID": 840350000533592
}
```

#### a. Field name mapping

For the subset visible in your sheets, the mapping is:

- `SQL.Name` / `EXPORT.Name` → **API property name (after case normalization / prefix)**
    - `deleteddate` → `DeletedDate` in API
    - `dsuid` → `DSUID`
    - `envbasin` → `ENVBasin`
    - `envcounty` → `ENVCounty`
    - `envinterval` → `ENVInterval`

The API is essentially:

- Taking the base name from the `Name` column.
- Applying a case convention:
    - `env...` becomes `ENV...`
    - `pw...` becomes `PW...`
    - multi‑word fields become PascalCase: `deleteddate` → `DeletedDate`, `updateddate` → `UpdatedDate`.
- Some boolean/ID‑like fields are all caps acronym style (e.g., `DSUID`).

This is closer in spirit to the **EXPORT** schema than SQL, because API consumers care about nice names and stable types, not internal geodatabase flags.

#### b. Types and formats

- In SQL/EXPORT:
    - `deleteddate` and likely `updateddate` are type `Date`.
- In the API:
    - These are emitted as **ISO 8601 UTC datetime strings**, e.g. `2021-02-05T11:19:48Z`.
- Strings like `envbasin`, `envcounty`, `envinterval` are strings in both table schemas and API.
- Numeric fields (e.g., `ENVPWSpacingScenario`, `LateralLength`, `WellID`) should correspond to `Integer`/`Double` types defined elsewhere in the same sheets.

So the API is essentially _serializing_ the same underlying types:

- Date → ISO datetime string
- String → string
- Numeric → number

#### c. Aliases vs API property names

- `EXPORT.Alias` is for _display labels_ (e.g., UI headers):
    - `EnvBasin` alias `Basin`
    - `dsuid` alias `DSU ID`
    - `deleteddate` alias `Deleted Date`
- The API property names are **not** the aliases; they are derived from the internal `Name`:
    - `dsuid` → `DSUID`, not `DSU ID`
    - `envbasin` → `ENVBasin`, not `Basin`

So:

- **Aliases** ≈ human‑readable column headers in GIS/Table UIs.
- **API property names** ≈ stable, code‑friendly identifiers derived from `Name`.

#### d. Additional fields in API not visible in your preview

The API example includes more fields than the first few shown in the sheets:

- `ENVPWOperator`, `ENVPWSpacingScenario`, `ENVPeerGroup`, `ENVPlay`, `ENVRegion`, `ENVSubPlay`, `ENVWellStatus`, `Geom_WKT`, `LateralLength`, `PWAPIUWI`, `PWAzimuthFromGridNorth`, `PWCoordinateSource`, `PWLatitude`, `PWLongitude`, `PredictedOperatorSource`, `StateProvince`, `Trajectory`, `UpdatedDate`, `WellID`.

Those will correspond to additional rows further down in the `SQL` and `EXPORT` sheets, with:

- `Name` like `envpwoperator`, `envpwspacingscenario`, `envpeergroup`, etc.
- Types `String`, `Integer`, `Double`, or `Geometry/Shape` (for `Geom_WKT`).
- Similar alias and constraint differences between SQL vs EXPORT.

---

### 3. How to think about this in a “schema/contract” sense

#### Role of the `SQL` sheet

- Internal geodatabase/SQL design.
- Defines:
    - Canonical field names (`Name`)
    - Base data types (`Type`)
    - Nullability and editing rules (`Nullable`, `Editable`, `Required`)
- Less concerned with:
    - User-facing alias text
    - Final column ordering for exports

#### Role of the `EXPORT` sheet

- Export / API schema configuration.
- Adds/refines:
    - **User-friendly aliases** (for maps/reports/UIs)
    - **Usage roles** (e.g., index, ID, etc.)
    - **String lengths and numeric precision** tuned for distribution
    - **Geodatabase Order** that dictates column order in exported feature classes/tables.
- Conceptually the **closest** to your external contract (the API), but still GIS-centric.

#### Role of the API record

- Final _public contract_ to developers.
- Uses:
    - Property names derived from `Name` (with specific casing/prefix rules).
    - Data types and semantics as defined in SQL/EXPORT.
    - Date fields serialized as ISO 8601 strings.
- Ignores:
    - Aliases (they do not appear in JSON).
    - Internal geodatabase ordering; JSON is conceptually unordered.

---

### 4. Practical implications / guidance

#### For comparing and validating schemas

- You can treat `SQL.Name` as the **source of truth** for logical field names.
- Confirm that every API property:
    - Has a corresponding `Name` in SQL/EXPORT.
    - Has a consistent logical type:
        - Date in SQL/EXPORT → ISO string in API.
        - String → string.
        - Numeric → number.
- Where `EXPORT` differs from `SQL` (lengths/usage/order), consider EXPORT the “public export spec” you want the API to remain consistent with.

#### For documentation and developer guidance

- Document **field-level mapping** like:
    
    - `SQL.Name = deleteddate` `EXPORT.Alias = Deleted Date` API property = `DeletedDate` Type = Date → ISO 8601 string
        
    - `SQL.Name = dsuid` `EXPORT.Alias = DSU ID` API property = `DSUID` Type = String
        
- This helps internal GIS folks, DBAs, and API developers speak the same language.
    

---

### 5. Concise report summary

- **SQL vs EXPORT**
    - Same fields (`Name`), same base types.
    - EXPORT has more polished aliases, specific lengths/precision, usage roles, and a different geodatabase order for export.
- **EXPORT vs API**
    - API property names are derived from the `Name` values (with case and prefix conventions), not from `Alias`.
    - Types map directly: strings, numbers, and dates (dates serialized as ISO 8601).
    - EXPORT is effectively a “staging schema” for external consumption; API is the JSON representation of that same logical schema.
- **Consistency**
    - The sample API record is consistent with the SQL/EXPORT design you’ve provided, particularly for `DeletedDate`, `DSUID`, `ENVBasin`, `ENVCounty`, and `ENVInterval`.