
# Transfering Attachments Select Polygon boundaries

1. **Create both DB connections**
    
    - In **Catalog** pane → **Folders/Databases** → **Add Database**.
        
    - Connect to:
        
        - **Prod SDE** (contains `old_fc`)
            
        - **Dev SDE** (will hold `new_fc`)
            
    - Verify you can see both FCs.
        
2. **Both FCs must have GlobalIDs**
    
    - In **Catalog** → right-click FC → **Manage** → confirm **Global IDs** exist.
        
    - If not, run: **Data Management → Fields → Add Global IDs**.
        
    
    _arcpy (optional):_
    
    `arcpy.management.AddGlobalIDs(r"Prod.sde\schema.old_fc") arcpy.management.AddGlobalIDs(r"Dev.sde\schema.new_fc")`
    
3. **Attachments enabled?**
    
    - Old FC should already have attachments if you’re migrating them.
        
    - New FC: if you’ll attach, enable attachments now.  
        **Data Management → Attachments → Enable Attachments** (on `new_fc`).
        
    
    _arcpy:_
    
    `arcpy.management.EnableAttachments(r"Dev.sde\schema.new_fc")`
    
4. **Lock your join keys**
    
    - Decide your **stable match field(s)** shared between old & new (e.g., `FacilityID`, `SiteName`, `AssetID`, or a composite).
        
    - If the schema changed, you can still create a synthetic key (see §3C).
        

---

# 1) Load/append data into the NEW FC (Dev)

If your `new_fc` schema is different, use **Append** with field mapping.

**GUI:**

- **Data Management → General → Append**
    
    - **Input Datasets:** `Prod.sde\schema.old_fc`
        
    - **Target Dataset:** `Dev.sde\schema.new_fc`
        
    - **Schema Type:** `NO_TEST` (lets you map fields)
        
    - Click **Field Map**:
        
        - Map old → new attribute fields carefully (domains/subtypes okay).
            
- If your schema is identical: use `TEST` and skip mapping.
    

_Notes_

- **GlobalIDs** in `new_fc` will be **newly generated** (that’s what we want for a crosswalk).
    
- Do **not** try to copy the old GlobalIDs into the new GlobalID field—ArcGIS won’t allow it (system-managed).
    

_arcpy:_

`arcpy.management.Append(     inputs=[r"Prod.sde\schema.old_fc"],     target=r"Dev.sde\schema.new_fc",     schema_type="NO_TEST"  # change to TEST if identical schema     # You can also pass field_mappings if you prebuild them )`

---

# 2) Export the old attachments to disk + table

You need the actual image/docs on disk and a table that lists each file’s `REL_GLOBALID` (the GlobalID of the source feature in old FC).

**GUI:**

- **Data Management → Attachments → Export Attachments**
    
    - **Input Dataset:** `Prod.sde\schema.old_fc`
        
    - **Output Folder:** a clean folder, e.g., `D:\migrate\old_fc_attachments\files`
        
    - **Output Table:** a file gdb table, e.g., `D:\migrate\work\migrate.gdb\old_attach_export`
        
    - Leave other defaults.
        

Resulting table typically has columns like:

- `REL_GLOBALID` (links attachment → old feature)
    
- `ATT_NAME` (filename on disk)
    
- `CONTENT_TYPE`, `DATA` (DATA unused since you exported files)
    

_arcpy:_

`arcpy.management.ExportAttachments(     in_dataset=r"Prod.sde\schema.old_fc",     out_folder=r"D:\migrate\old_fc_attachments\files",     out_table=r"D:\migrate\work\migrate.gdb\old_attach_export" )`

---

# 3) Build the crosswalk (OLD GlobalID → NEW GlobalID)

Your goal is a table with **two columns** at minimum:

- `OLD_GLOBALID` (GlobalID from old FC)
    
- `NEW_GLOBALID` (GlobalID from new FC)  
    …and later we’ll add/attach `ATT_NAME`.
    

There are a few reliable ways to produce this mapping:

## 3A) Straight attribute join (best if you have a single stable key)

**GUI:**

1. Add **both** FCs to a blank map.
    
2. Right-click `new_fc` → **Joins and Relates → Add Join…**
    
    - **Layer:** `new_fc`
        
    - **Input Join Field (new):** e.g., `FacilityID`
        
    - **Join Table:** `old_fc`
        
    - **Output Join Field (old):** `FacilityID`
        
    - Check “Keep all target features” if you want all new features retained.
        
3. Now **Export** the joined layer to a table:
    
    - Right-click joined `new_fc` layer → **Data → Export Table**
        
    - Output: `D:\migrate\work\migrate.gdb\crosswalk_raw`
        
    - In **Field Map**, include:
        
        - `old_fc.GLOBALID` → rename to `OLD_GLOBALID`
            
        - `new_fc.GLOBALID` → rename to `NEW_GLOBALID`
            
        - (Optionally) include your join key(s) to troubleshoot duplicates.
            
4. **Clean** duplicates if needed (see §3D).
    

## 3B) Composite key (if one field isn’t unique)

Create a **calculated key** in both FCs that concatenates attributes (e.g., `FacilityID + "_" + State + "_" + SiteName`), then join on that.

- Add a new text field `JoinKey` in both FCs.
    
- **Field Calculator**:
    
    `!FacilityID! + "_" + !State! + "_" + !SiteName!`
    
- Then repeat **3A** joining on `JoinKey`.
    

## 3C) Spatial join (fallback if attributes aren’t stable)

If locations didn’t change much:

- **Analysis → Overlay → Spatial Join**
    
    - **Target Features:** `new_fc`
        
    - **Join Features:** `old_fc`
        
    - **Match Option:** `CLOSEST` (or `INTERSECT` if appropriate)
        
    - Output: `crosswalk_spatial`
        
    - Keep fields: `old_fc.GLOBALID` (→ `OLD_GLOBALID`), `new_fc.GLOBALID` (→ `NEW_GLOBALID`)
        
- Review distances to ensure correctness.
    

## 3D) De-duplication & validation (important)

Open the crosswalk output and check:

- **1:1 cardinality** between old and new.
    
- No nulls in either `OLD_GLOBALID` or `NEW_GLOBALID`.
    
- If duplicates exist (same `OLD_GLOBALID` mapping to multiple `NEW_GLOBALID`s or vice-versa), resolve them by:
    
    - Filtering on your keys
        
    - Manually reviewing problem rows
        
    - Re-running join with tighter keys or spatial buffer rules.
        

_arcpy pattern to extract a clean pair table once you have a joined layer:_

`# Suppose you created a joined view called 'new_fc_join' arcpy.conversion.TableToTable(     in_rows="new_fc_join",     out_path=r"D:\migrate\work\migrate.gdb",     out_name="crosswalk_raw",     field_mapping='OLD_GLOBALID "OLD_GLOBALID" true true false 38 GUID 0 0,First,#,old_fc,GLOBALID,-1,-1;NEW_GLOBALID "NEW_GLOBALID" true true false 38 GUID 0 0,First,#,new_fc,GLOBALID,-1,-1' )`

---

# 4) Combine crosswalk with exported attachment table

We need the attachment filenames (`ATT_NAME`) lined up with the **NEW** GlobalIDs.

**Goal:** a final **match table** with:

- `NEW_GLOBALID`
    
- `ATT_NAME`
    

**Join path:**

- `old_attach_export.REL_GLOBALID` → `crosswalk_raw.OLD_GLOBALID`
    
- Output will have `NEW_GLOBALID` + `ATT_NAME`.
    

**GUI:**

1. Add tables `old_attach_export` and `crosswalk_raw` to the map.
    
2. **Add Join**:
    
    - **Layer/Table:** `old_attach_export`
        
    - **Input Join Field:** `REL_GLOBALID`
        
    - **Join Table:** `crosswalk_raw`
        
    - **Output Join Field:** `OLD_GLOBALID`
        
3. **Export Table**:
    
    - Right-click the joined `old_attach_export` → **Data → Export Table**
        
    - Output: `D:\migrate\work\migrate.gdb\attach_match`
        
    - Keep only:
        
        - `crosswalk_raw.NEW_GLOBALID` (rename to `NEW_GLOBALID`)
            
        - `old_attach_export.ATT_NAME` (keep as `ATT_NAME`)
            
    - (Optional) keep `CONTENT_TYPE` for QA.
        

_arcpy:_

`# Join exported attachments to crosswalk on old GlobalID arcpy.management.AddJoin(     in_layer_or_view=r"D:\migrate\work\migrate.gdb\old_attach_export",     in_field="REL_GLOBALID",     join_table=r"D:\migrate\work\migrate.gdb\crosswalk_raw",     join_field="OLD_GLOBALID",     join_type="KEEP_COMMON" ) arcpy.conversion.TableToTable(     in_rows=r"D:\migrate\work\migrate.gdb\old_attach_export",     out_path=r"D:\migrate\work\migrate.gdb",     out_name="attach_match",     field_mapping='NEW_GLOBALID "NEW_GLOBALID" true true false 38 GUID 0 0,First,#,crosswalk_raw,NEW_GLOBALID,-1,-1;ATT_NAME "ATT_NAME" true true false 255 Text 0 0,First,#,old_attach_export,ATT_NAME,0,255' ) # (Optional) Remove Join after export arcpy.management.RemoveJoin(r"D:\migrate\work\migrate.gdb\old_attach_export")`

---

# 5) Reattach to the NEW FC (Dev) with “Add Attachments”

Now you have:

- The **files** on disk in `...old_fc_attachments\files\`
    
- A **match table** `attach_match` with `NEW_GLOBALID` + `ATT_NAME`
    

**GUI:**

- **Data Management → Attachments → Add Attachments**
    
    - **Input Dataset:** `Dev.sde\schema.new_fc`
        
    - **Input Join Field:** `GLOBALID` _(of new_fc)_
        
    - **Match Table:** `D:\migrate\work\migrate.gdb\attach_match`
        
    - **Match Join Field:** `NEW_GLOBALID`
        
    - **Match Attachments Folder:** `D:\migrate\old_fc_attachments\files`
        
    - **Match File Field:** `ATT_NAME`
        
    - Run.
        

_arcpy:_

`arcpy.management.AddAttachments(     in_dataset=r"Dev.sde\schema.new_fc",     in_field="GLOBALID",     match_table=r"D:\migrate\work\migrate.gdb\attach_match",     match_join_field="NEW_GLOBALID",     attach_folder=r"D:\migrate\old_fc_attachments\files",     attach_name_field="ATT_NAME" )`

---

# 6) QA & reconciliation

1. **Counts check**
    
    - Count rows in `old_fc__ATTACH` (Prod) vs `new_fc__ATTACH` (Dev).
        
        - In Catalog, open the attach tables and compare counts.
            
    - If counts differ, identify misses:
        
        - Join `old_attach_export` to `attach_match` (left join) and select where `NEW_GLOBALID` is null → those didn’t map.
            
        - Fix the crosswalk and rerun **Add Attachments** for those missed files only.
            
2. **Spot check**
    
    - Pick a few features; open the **Attributes** pane → **Attachments** and visually confirm.
        
3. **Broken paths**
    
    - If Add Attachments errors on missing files: verify `ATT_NAME` values exist in the folder and no extra subfolder path is needed. (If subfolders were created, you may need to prepend a relative folder in a new field; the tool uses `Match Attachments Folder` + `ATT_NAME`.)
        
4. **Duplicates**
    
    - If you see duplicates in `new_fc__ATTACH`, dedupe by:
        
        - Rebuilding a clean `attach_match` (distinct by `NEW_GLOBALID+ATT_NAME`) and re-adding only missing ones, or
            
        - Removing dup rows from the attach table (careful; keep backups).
            

---

# 7) Move the finished NEW FC to final DB (when ready)

When Dev is validated:

**Option A: Simple copy**

- **Data Management → Copy Features** from Dev to Final SDE.
    
    - Attachments go with it if enabled.
        
    - GlobalIDs retained.
        

**Option B: Feature Class To Feature Class**

- Use **Feature Class To Feature Class** to Final SDE.
    
- Ensure **attachments are preserved** (they are when moving within geodatabases and attachments are enabled).
    

_arcpy:_

`arcpy.conversion.FeatureClassToFeatureClass(     in_features=r"Dev.sde\schema.new_fc",     out_path=r"Final.sde",     out_name="schema.new_fc" )`

---

# 8) Troubleshooting cookbook

- **“Add Attachments” says it can’t find files**
    
    - Confirm `Match Attachments Folder` is exactly where the exported files sit.
        
    - Confirm `ATT_NAME` values (no trailing spaces; fix with a Calculate Field `strip()` in a text field).
        
- **Join key mismatches**
    
    - Use **Calculate Field** to force consistent casing/whitespace:
        
        - `!SiteName!.strip().upper()`
            
- **Non-unique join keys**
    
    - Use composite keys (see §3B) or **Spatial Join** fallback (§3C).
        
- **Attachments exist in old but not exported**
    
    - Confirm attachments truly live in `old_fc__ATTACH`.
        
    - Re-run **Export Attachments** with correct `old_fc`.
        
- **Performance**
    
    - Do the heavy joins & exports in a **file gdb** on local disk, not across network paths.
        

---

# 9) One-click arcpy pipeline (optional)

Paste into **Python** window and adjust paths/field names:

`import arcpy, os  prod_old = r"Prod.sde\schema.old_fc" dev_new  = r"Dev.sde\schema.new_fc" wrk_gdb  = r"D:\migrate\work\migrate.gdb" out_folder = r"D:\migrate\old_fc_attachments\files"  # 0) Ensure GlobalIDs & attachments if "ATTACHRELATIONSHIPCLASS" not in [d.name.upper() for d in arcpy.Describe(dev_new).children]:     arcpy.management.EnableAttachments(dev_new)  # 1) Append old → new (skip if already done) # arcpy.management.Append([prod_old], dev_new, "NO_TEST")  # 2) Export attachments arcpy.management.ExportAttachments(prod_old, out_folder, os.path.join(wrk_gdb, "old_attach_export"))  # 3) Build crosswalk via attribute join on FacilityID (edit field to your key) # Make views arcpy.management.MakeFeatureLayer(dev_new, "newLyr") arcpy.management.AddJoin("newLyr", "FacilityID", prod_old, "FacilityID", "KEEP_COMMON")  # Export crosswalk_raw with only two GUIDs fm = arcpy.FieldMappings() for src, name in [(dev_new, "GLOBALID"), (prod_old, "GLOBALID")]:     fm.addTable(src) # Reduce to just the two fields and rename for f in fm.fields:     if f.name.upper().endswith(".GLOBALID"):         if f.name.lower().startswith("schema_old_fc"):             f.aliasName = "OLD_GLOBALID"         else:             f.aliasName = "NEW_GLOBALID" # (Simplify by rebuilding mapping explicitly if needed)  arcpy.conversion.TableToTable("newLyr", wrk_gdb, "crosswalk_raw")  # 4) Join exported attachments to crosswalk on old GlobalID arcpy.management.MakeTableView(os.path.join(wrk_gdb, "old_attach_export"), "attachView") arcpy.management.AddJoin("attachView", "REL_GLOBALID", os.path.join(wrk_gdb, "crosswalk_raw"), "OLD_GLOBALID", "KEEP_COMMON")  # Export match table with NEW_GLOBALID + ATT_NAME arcpy.conversion.TableToTable("attachView", wrk_gdb, "attach_match")  # 5) Add attachments to new_fc arcpy.management.AddAttachments(     dev_new,     "GLOBALID",     os.path.join(wrk_gdb, "attach_match"),     "NEW_GLOBALID",     out_folder,     "ATT_NAME" )`

> If your join key isn’t `FacilityID`, change that in the code and in the GUI steps above.

---

# 10) Validation checklist (print this)

- [ ]  `new_fc` record count equals (or intentionally differs from) `old_fc` after transformation.
    
- [ ]  `new_fc` has **GlobalIDs** and **attachments enabled**.
    
- [ ]  `old_attach_export` row count equals `old_fc__ATTACH` count.
    
- [ ]  `attach_match` row count equals `old_attach_export` (minus any intentionally excluded files).
    
- [ ]  `new_fc__ATTACH` row count equals `old_fc__ATTACH` (± expected changes).
    
- [ ]  Spot-checked 5–10 records; attachments display correctly.
    
- [ ]  No duplicate or orphan attachments found in `new_fc__ATTACH`.
    
- [ ]  Documented your final **join rule(s)** and any manual fixes.





# Create Point FC from Schema 


Natalie Review