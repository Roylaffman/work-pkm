# 📘 Enverus Schema Excel Instructions & Documentation


Table Plus key:
`TP-84FC4747-A31E-4B40-BF52-C1C4310E877A`

Video 1
[Recap: Enverus Wednesday, October 1 | Meeting | Microsoft Teams](https://teams.microsoft.com/l/meetingrecap?driveId=b%21TsBQTrKKu0WuGZAiYeOfDwo39BMLwPhNih6UDNRLlWT4B3kpowPATKF07QxDHzCt&driveItemId=01FQFTDR7PWUFL7XKHZVELNYN5GH6NTIAN&sitePath=https%3A%2F%2Fselectenergyservices-my.sharepoint.com%2F%3Av%3A%2Fg%2Fpersonal%2Fslangley_selectwater_com%2FEe-1Cr_dR81ItuG9MfzZoA0BXJFcAdv8srsvF18MYfKA-Q&fileUrl=https%3A%2F%2Fselectenergyservices-my.sharepoint.com%2Fpersonal%2Fslangley_selectwater_com%2FDocuments%2FRecordings%2FEnverus-20251001_101330-Meeting%2520Recording.mp4%3Fweb%3D1&iCalUid=040000008200E00074C5B7101A82E008000000009A1F73E3DA32DC01000000000000000010000000E066174ABD8E9A4CB8C447222147E054&threadId=19%3Ameeting_YzlkNjVjZWEtYWU0YS00NDZiLWI3YTctMWE1NGNiODhjOWFl%40thread.v2&organizerId=a80953d6-e4de-42cb-9f5c-66f46cf72442&tenantId=79cbfba5-ed84-4dd1-9edf-ae1c6b3a388d&callId=14f64118-9e1a-451f-be6c-2fd2a8464d89&threadType=Meeting&meetingType=Scheduled&subType=RecapSharingLink_RecapCore "https://teams.microsoft.com/l/meetingrecap?driveId=b%21TsBQTrKKu0WuGZAiYeOfDwo39BMLwPhNih6UDNRLlWT4B3kpowPATKF07QxDHzCt&driveItemId=01FQFTDR7PWUFL7XKHZVELNYN5GH6NTIAN&sitePath=https%3A%2F%2Fselectenergyservices-my.sharepoint.com%2F%3Av%3A%2Fg%2Fpersonal%2Fslangley_selectwater_com%2FEe-1Cr_dR81ItuG9MfzZoA0BXJFcAdv8srsvF18MYfKA-Q&fileUrl=https%3A%2F%2Fselectenergyservices-my.sharepoint.com%2Fpersonal%2Fslangley_selectwater_com%2FDocuments%2FRecordings%2FEnverus-20251001_101330-Meeting%2520Recording.mp4%3Fweb%3D1&iCalUid=040000008200E00074C5B7101A82E008000000009A1F73E3DA32DC01000000000000000010000000E066174ABD8E9A4CB8C447222147E054&threadId=19%3Ameeting_YzlkNjVjZWEtYWU0YS00NDZiLWI3YTctMWE1NGNiODhjOWFl%40thread.v2&organizerId=a80953d6-e4de-42cb-9f5c-66f46cf72442&tenantId=79cbfba5-ed84-4dd1-9edf-ae1c6b3a388d&callId=14f64118-9e1a-451f-be6c-2fd2a8464d89&threadType=Meeting&meetingType=Scheduled&subType=RecapSharingLink_RecapCore")
## 🎯 Objective

Create Excel schema templates that mirror the Enverus data model (tables + feature classes), which will serve as blueprints for:

- Building tables in the Oil & Gas Enterprise Geodatabase (Postgres + ESRI).
    
- Feeding into Dagster/DBT pipelines for automation.
    
- Ensuring consistency in field datatypes, lengths, relationships, and naming.
    

---

## 🗂️ Step 1. Excel Workbook Setup

- **One sheet per table/feature class group** (e.g., Wells, Permits, Production, Spacing Units).
    
- **One sheet for Relationships** to map keys and dependencies.
    

### Standard Schema Sheet Columns

|Table Name|Field Name|Field Alias|Data Type|Field Length / Precision|Nullable (Y/N)|Default Value|Key (Y/N)|Geometry Type (if feature class)|Notes|
|---|---|---|---|---|---|---|---|---|---|

---

## 🔍 Step 2. Determining Field Lengths & Datatypes

Meeting notes stressed:

- **Correct way**: Pull all data → query maximum field length → set schema accordingly.
    
- **Practical way**: Use known standards for common fields (e.g., API numbers → 32 chars).
    
- **Iterative approach**: Start with guessed lengths, validate by checking data again.
    

👉 Action for you:

- Use **TablePlus** to query `max(length(field))` for varchar fields.
    
- For known fields (API, UWI, lat/long) → apply standard lengths.
    
- Document assumptions in the **Notes** column.
    

---

## 🧩 Step 3. Populating Schemas

- Inspect **sde/gisadmin schemas** in TablePlus.
    
- In **ArcGIS Pro**, identify:
    
    - Tables with a `Shape` field → **Feature Classes** (Point, Line, Polygon).
        
    - Tables without a `Shape` field → **Attribute Tables**.
        
- Enter all fields into Excel with:
    
    - Human-readable aliases.
        
    - Enverus-conformant data types.
        
    - Geometry type (if applicable).
        

---

## 🔗 Step 4. Relationships Sheet

Meeting notes emphasized **dependencies**:

- Tables with no dependencies can be renamed/restructured freely.
    
- Tables with dependencies must be coordinated with the POC (Ken, Greg, David).
    
- Rename/update DAGS in sync with schema changes.
    

### Relationship Sheet Example

|Origin Table|Origin Key|Destination Table|Destination Key|Relationship Type|Cardinality|Notes|
|---|---|---|---|---|---|---|
|wells|well_id|production|well_id|Foreign Key|1-M|Validate with DAG updates|

---

## 🔄 Step 5. Naming & Conventions

- Use **snake_case**, lowercase, no spaces (e.g., `well_production`).
    
- Match Enverus field sizes and datatypes exactly.
    
- Human-readable field aliases for ArcGIS Pro.
    
- Track `Nullable` carefully (non-null fields must be populated at ingestion).
    

---

## 📊 Step 6. Deliverables

1. **Excel Workbook** containing:
    
    - Tabs for each table/feature class.
        
    - Relationships tab.
        
    - Notes on field lengths, assumptions, and dependencies.
        
2. **Documentation page** summarizing:
    
    - How to query max field lengths in TablePlus.
        
    - Standards (API = 32 chars, etc.).
        
    - How dependencies must be coordinated with DAG updates and POCs.
        

---

## ✅ Checklist Before Handoff

-  All Enverus tables mirrored in Excel with correct datatypes.
    
-  Max field lengths verified (or documented if assumptions used).
    
-  Geometry type noted for each feature class.
    
-  Relationships tab complete (keys + dependencies).
    
-  Naming conventions consistent with standards.
    
-  Workbook reviewed with Natalie/Jose before DAG ingestion.
    

---

📌 **Key Meeting Takeaways Added:**

- Use **max length queries** for field size validation.
    
- Standardize known fields (API numbers → 32 chars).
    
- Rename tables only if no dependencies; otherwise coordinate with POCs + DAG updates.



Once in table plus go to gisadmin and find the enverus tables:
![[Table Plus Enverus.png]]


Create/use Sheets for each FC/table that needs to be varified this will be every table/feature without the word futur efor now and then some.


# How to 2

# 🔹 Step 1. Gather Schema Requirements

- **Source**: Enverus ERD / API schema dump (tables, fields, datatypes, relationships).
    
- **Target**: Oil & Gas Enterprise Geodatabase (Postgres + ESRI).
    
- **Goal**: Mirror Enverus’ structure but in your company’s environment.
    

👉 Deliverable:  
An Excel workbook with one tab per **feature class/table group** (e.g., Wells, Permits, Production, Spacing Units).

---

# 🔹 Step 2. Design Excel Schema Template

For each tab in Excel, define columns like:

|Table Name|Field Name|Field Alias|Data Type|Field Length/Precision|Nullable (Y/N)|Default Value|Key (Y/N)|Geometry Type (if feature class)|Notes|
|---|---|---|---|---|---|---|---|---|---|
|well|well_id|Well ID|Integer|n/a|N|n/a|PK|Point|Unique identifier|
|well|api|API Number|String|14|N|n/a||Point|Standard API format|

**Tips**:

- Geometry types → mark as `Point`, `Line`, or `Polygon` if it’s a feature class.
    
- Field Alias → make them human-readable for ArcGIS Pro.
    
- Keys → mark primary/foreign keys based on Enverus ERD (important for later relationship classes).
    

👉 Deliverable:  
A **standard Excel template** your team can reuse for any dataset (you just fill rows for each Enverus table/feature).

---

# 🔹 Step 3. Populate From Enverus + EGIS

- Use **TablePlus** to inspect tables in your current `oilandgas` DB. Note:
    
    - Field names and types (`character varying`, `integer`, `numeric`, `geometry(Point, 4326)`, etc.).
        
    - System schemas like `sde`, `gisadmin` — your tables will live under `sde` if it’s an ArcGIS Enterprise Geodatabase.
        
- Use **ArcGIS Pro** to check which tables are **feature classes** (point/line/polygon) vs. pure attribute tables.
    
    - If you see Shape field → it’s a feature class.
        
    - No Shape field → it’s a supporting table.
        

👉 Deliverable:  
Populate Excel sheets with the real **datatypes and geometry types** as confirmed in TablePlus/ArcGIS Pro.

---

# 🔹 Step 4. Map Relationships

In a separate Excel tab (e.g., `Relationships`), capture:

|Origin Table|Origin Key|Destination Table|Destination Key|Relationship Type|Cardinality (1-1, 1-M, M-M)|Notes|
|---|---|---|---|---|---|---|
|well|well_id|production|well_id|Foreign Key|1-M|Each well has many production records|

👉 Deliverable:  
Clear mapping of how tables/feature classes relate (so later you/DBA can build ArcGIS relationship classes).

---

# 🔹 Step 5. Validation & Conventions

- Ensure table/field **naming conventions** (lowercase, underscores, no spaces).
    
- Match **Enverus field sizes** exactly (Excel = your checklist for when creating in Postgres).
    
- Ensure **human-readable aliases** for ArcGIS.
    
- Track **nullable fields** carefully (helps avoid import errors).
    

👉 Deliverable:  
An Excel workbook with consistent, validated field specs ready for import.

---

# 🔹 Step 6. Hand-off to Dagster Team

- Provide the Excel workbook as the **blueprint**.
    
- Dagster/DBT can then auto-generate tables/feature classes from your schema.
    
- You can also script a schema validation step: compare Enverus schema dump vs. your Excel → flag mismatches.
    

---

# ✅ Final Deliverables From You

1. **Excel Workbook** with:
    
    - One sheet per Enverus table/feature class schema.
        
    - One sheet documenting relationships (ERD mapping).
        
    - Notes on geometry type & field alias.
        
2. **Documentation (1–2 page guide)** explaining how to read/use the Excel schema (so Dagster engineers know how to translate it