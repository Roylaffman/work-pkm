


at question—here’s the short take then the full how-to + a drop-in ArcPy script to copy all attachments from the **old FC (SELECT DB)** to your **new FC (DEV DB)**.

## 1) Where to do the attachment copy?

Do it **now in DEV**. Reasons:

- You can QC that geometry, attributes, **and attachments** all line up before touching production.
    
- When you later promote the dataset from DEV → SELECT, copy it **with GlobalIDs preserved** so attachment links remain intact. (Details at the end.)
    

## 2) Step-by-step (best practice)

1. **Pick a stable key** shared by old and new features (e.g., `boundary_id`).  
    If you truly preserved GlobalIDs during your attribute/geometry copy, you can map by `GLOBALID`; otherwise use your business key.
    
2. On **both** FCs:
    
    - Ensure **GlobalIDs** exist (`AddGlobalIDs` if needed).
        
    - Ensure **attachments are enabled** (target must have attachments enabled).
        
3. Build a **mapping** from your key → target `GLOBALID`.
    
4. Read attachments from the old FC’s `__ATTACH` table (by `REL_GLOBALID`) and **insert** them into the new FC’s `__ATTACH` table using the matched target `GLOBALID`.
    
5. **QC** a handful of features in Pro to confirm attachments appear and open.
    
6. When promoting DEV → SELECT, **preserve GlobalIDs** so the attachment links remain valid.
    

---

## 3) ArcPy script: copy all attachments (SELECT → DEV)

- Uses `boundary_id` as the match key.
    
- If you instead preserved `GLOBALID`s, set `MATCH_ON_GLOBALID = True`.
    

`import arcpy  # ----------------- SETTINGS ----------------- # Source (old, SELECT DB) src_sde = r"D:\Users\rlafferty\AppData\Roaming\Esri\ArcGISPro\Favorites\Selectwater.sde" src_fc_name = "select_boundary_polygons"  # Target (new, DEV DB) tgt_sde = r"D:\Users\rlafferty\AppData\Roaming\Esri\ArcGISPro\Favorites\PostgreSQL-gisdb-select_gis_dev(rlafferty).sde" tgt_fc_name = "select_boundary_polygons"  # Matching strategy MATCH_ON_GLOBALID = False              # set True if GlobalIDs are identical in both FCs BUSINESS_KEY_FIELD = "boundary_id"     # used when MATCH_ON_GLOBALID is False  # Classic editor tracking field names are irrelevant here, listed just for context # creator_field="created_user"; creation_date_field="created_date"; ...  # ----------------- DERIVED PATHS ----------------- src_fc = fr"{src_sde}\{src_fc_name}" tgt_fc = fr"{tgt_sde}\{tgt_fc_name}"  src_attach = fr"{src_fc}__ATTACH" tgt_attach = fr"{tgt_fc}__ATTACH"  # ----------------- HELPERS ----------------- def ensure_globalids(path):     fields = [f.name.lower() for f in arcpy.ListFields(path)]     if "globalid" not in fields:         arcpy.management.AddGlobalIDs(path)  def ensure_attachments(fc):     # If attachments not enabled, EnableAttachments will create the __ATTACH table and relationship     desc = arcpy.Describe(fc)     has_atts = getattr(desc, "hasAttachments", False)     if not has_atts:         arcpy.management.EnableAttachments(fc)  # ----------------- MAIN ----------------- try:     print("=== Attachment Transfer: SELECT → DEV ===")     print(f"Source FC: {src_fc}")     print(f"Target FC: {tgt_fc}")      # 1) Ensure prerequisites     ensure_globalids(src_fc)     ensure_globalids(tgt_fc)     ensure_attachments(tgt_fc)      # 2) Build target match index     print("Building target match index...")     tgt_map = {}  # key -> target GLOBALID      if MATCH_ON_GLOBALID:         with arcpy.da.SearchCursor(tgt_fc, ["GLOBALID"]) as cur:             for (tgt_gid,) in cur:                 tgt_map[str(tgt_gid).upper()] = tgt_gid     else:         # Map by business key -> GLOBALID         with arcpy.da.SearchCursor(tgt_fc, [BUSINESS_KEY_FIELD, "GLOBALID"]) as cur:             for key, tgt_gid in cur:                 if key is None:                     continue                 tgt_map[str(key)] = tgt_gid      print(f"Target index size: {len(tgt_map)}")      # 3) Build source feature index for quick lookup of src feature key -> src GLOBALID     print("Indexing source features...")     src_index = []  # list of tuples (src_gid, key_for_match)     if MATCH_ON_GLOBALID:         with arcpy.da.SearchCursor(src_fc, ["GLOBALID"]) as cur:             for (src_gid,) in cur:                 src_index.append((src_gid, str(src_gid).upper()))     else:         with arcpy.da.SearchCursor(src_fc, ["GLOBALID", BUSINESS_KEY_FIELD]) as cur:             for src_gid, key in cur:                 if key is None:                     continue                 src_index.append((src_gid, str(key)))      print(f"Source features considered: {len(src_index)}")      # 4) Prepare insert cursor for target __ATTACH     #    Common attachment fields: REL_GLOBALID, ATT_NAME, CONTENT_TYPE, DATA     t_fields = ["REL_GLOBALID", "ATT_NAME", "CONTENT_TYPE", "DATA"]     s_fields = ["REL_GLOBALID", "ATT_NAME", "CONTENT_TYPE", "DATA"]      # Verify target attachment table exists (EnableAttachments created it)     arcpy.Exists(tgt_attach) or (_ for _ in ()).throw(Exception("Target attachment table missing."))      inserted = 0     skipped_no_match = 0      # 5) Stream over attachments by matching each src feature     # Build a quick lookup of src_gid -> list of attachments for efficiency (optional)     print("Transferring attachments...")     with arcpy.da.InsertCursor(tgt_attach, t_fields) as ins:         # For performance, iterate the source attachment table once and buffer in memory by REL_GLOBALID         src_atts_by_gid = {}          # Some older attachment tables also have REL_OBJECTID; we rely on REL_GLOBALID.         if not arcpy.Exists(src_attach):             raise Exception("Source attachment table not found. Are attachments enabled on the source?")          with arcpy.da.SearchCursor(src_attach, s_fields) as s_cur:             for rel_gid, att_name, mime, blob in s_cur:                 if rel_gid is None:                     continue                 src_atts_by_gid.setdefault(rel_gid, []).append((att_name, mime, blob))          # Iterate source features and push their attachments to the target         for src_gid, key in src_index:             # Find matching target GLOBALID             if MATCH_ON_GLOBALID:                 match = tgt_map.get(key)  # key is upper(src_gid)             else:                 match = tgt_map.get(key)  # key is business_key value as string              if not match:                 skipped_no_match += 1                 continue              # Get attachments for this source feature (by its GLOBALID)             atts = src_atts_by_gid.get(src_gid, [])             for att_name, mime, blob in atts:                 ins.insertRow((match, att_name, mime, blob))                 inserted += 1      print(f"✅ Attachments inserted: {inserted}")     if skipped_no_match:         print(f"ℹ️ Features without a match (attachments skipped): {skipped_no_match}")      print("Done.")  except Exception as e:     print(f"❌ Failed: {e}")`

### Important notes / promotion to SELECT

- **QC in DEV first.** Once you’re happy with content and attachments, promote from DEV → SELECT while **preserving GlobalIDs** so the attachment relationships remain valid.
    
- Ways to preserve GlobalIDs on final move:
    
    - In Python, set `arcpy.env.preserveGlobalIds = True` and use `arcpy.management.Copy` or `arcpy.management.Append` into the destination.
        
    - Or export/import an **XML Workspace Document** (this always preserves GlobalIDs and related classes, including the `__ATTACH` table).
        
- If you re-generate GlobalIDs during the move, attachment links will break (they key off `REL_GLOBALID`).
    

If you want me to flip the script to match on `GLOBALID` (if you kept them identical already), say the word and I’ll switch the flag and trim the key logic.