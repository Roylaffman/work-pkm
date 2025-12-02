
[[YAML Data Pipeline]]
# 1. **Simple Process Overview**

The documentation begins by describing two broad workflows:

---

## **📌 Workflow A: Creating a brand new data dictionary**

1. **Export CREATE TABLE statements from your database**  
    Pull schema definitions from Snowflake/Redshift/Postgres/etc.
    
2. **Convert this raw SQL into the CSV schema structure**  
    The utilities expect a CSV with specific columns like table_name, column_name, data_type, etc.
    
3. **Edit that CSV**  
    You add descriptions, metadata, ordering, etc.
    
4. **Convert CSV → YML**  
    Generates dbt-ready YAML source definitions.
    
5. **Import the generated YAML into dbt**  
    dbt docs will now show your full documentation.
    

---

## **📌 Workflow B: Updating existing source YML**

1. Export your existing dbt sources YAML
    
2. Convert YAML → CSV  
    This flattens the nested structure into a spreadsheet.
    
3. Edit the CSV in Excel/Sheets
    
4. Convert CSV → YML  
    Rebuilds the dbt YAML with your edits.
    
5. Replace/merge into dbt
    

This cycle is the **roundtrip** workflow (YML → CSV → edit → CSV → YML).

---

# 2. **Why this workflow exists**

The documentation highlights the real-world pain points:

### **YAML = great for dbt, terrible for editing**

- Hard to view 300+ columns
    
- Indentation-sensitive
    
- No sorting/filtering/find-replace
    
- Hard for non-engineers to contribute
    

### **CSV = perfect for bulk editing**

- Works with Excel/Google Sheets
    
- Fast find/replace patterns
    
- You can filter, sort, apply formulas
    
- Track diffs more easily in Git if needed
    
- Very easy for SMEs and analysts to collaborate on
    

These utilities combine the best of both:

- dbt uses YML
    
- humans edit CSV
    

---

# 3. **The Two Utilities**

The scripts are production-ready and handle all edge cases and metadata.

---

## **A. dbt_sources_yml_to_csv.py**

### **Purpose: Convert hierarchical YML → flat CSV**

This script takes a dbt `sources.yml` file and produces a CSV where each row represents:

`source_name / source_schema / table_name / column_name / data_type / description / constraints / order`

It also:

- Ensures a v0.7-compliant CSV schema
    
- Adds missing columns such as `table_description` or `column_order`
    

---

## **B. dbt_sources_csv_to_yml.py**

### **Purpose: Convert flat CSV → dbt YML**

Reverse of the above:

- Reads CSV
    
- Groups rows by source → table → columns
    
- Sorts columns using `column_order`
    
- Builds proper dbt YAML structure
    
- Writes a production-ready YAML file
    

The output is clean, readable, and deterministic.

---

# 4. **Schema Evolution (v0.6 → v0.7)**

The documentation explains the history of schema versions:

### **v0.6 introduced:**

- `table_description`
    

### **v0.7 introduced:**

- `column_order`
    

Both scripts automatically add these if missing.

This is important because:

- dbt doesn’t enforce a schema version for source YML, but _your utilities do_.
    
- The scripts help standardize documentation across the project.
    

---

# 5. **Installation Requirements**

`pip install pyyaml python 3.9+`

The scripts rely on standard Python and PyYAML.  
No external dependencies.

---

# 6. **Usage Examples**

Detailed command-line examples show how to call both scripts.

---

## **YML → CSV**

Example:

`python ScriptsProd/dbt_sources_yml_to_csv.py input.yml output.csv`

What it does:

1. Reads dbt YAML
    
2. Flattens sources → tables → columns into rows
    
3. Ensures CSV contains all v0.7 schema columns
    
4. Adds default table_description if missing
    
5. Assigns column_order values of 10, 20, 30…
    
6. Outputs clean CSV
    

---

## **CSV → YML**

Example:

`python ScriptsProd/dbt_sources_csv_to_yml.py input.csv output.yml`

What it does:

1. Validates the CSV has required fields
    
2. Groups by source, table, column
    
3. Sorts columns based on column_order
    
4. Generates dbt-compliant YAML
    

---

# 7. **CSV schema explained (v0.7)**

The CSV schema must include:

### **Required columns**

- source_name
    
- table_name
    
- column_name
    
- data_type
    

### **Optional (but recommended)**

- source_description
    
- source_database
    
- source_schema
    
- table_description
    
- column_order
    
- column_description
    
- constraint_type (comma-separated)
    

This allows the YAML to be reconstructed perfectly.

---

# 8. **Column Order Guidelines**

Column ordering controls how dbt docs render columns.

Recommended:

- Use increments of `10, 20, 30`
    
- Use `999` for “put this at the end”
    

Scripts automatically assign 10,20,30… if missing.

---

# 9. **Constraint Types**

CSV stores constraints as comma-separated:

`primary_key, not_null`

YAML uses:

`constraints:   - type: primary_key   - type: not_null`

The script normalizes/controls the formatting for consistency.

---

# 10. **Default Behaviors**

### **Default table description**

If missing, it generates:

`Table {schema}.{table} from {source} source.`

Example:

`Table aquacast_prod.dim_customer_aquacast from aquacast source.`

Useful as a skeleton you can fill in.

---

### **Default column_order behavior**

- YAML → CSV: auto-assign 10,20,30…
    
- CSV → YAML: missing values become 999
    

---

# 11. **Configuration Options**

Each script has a `Config` class with customizable behavior.

You can adjust:

- CSV column order
    
- Default table descriptions
    
- Indentation size
    
- How column order is assigned
    
- Expected/requred columns
    

This makes the utilities highly adaptable.

---

# 12. **Error Handling**

The scripts detect and report:

### ❌ Missing required CSV columns

E.g., if `column_name` is missing.

### ❌ Malformed YAML

If the yaml doesn't contain the root `sources:` key.

### ❌ Missing file paths

Clear error messages.

This makes the utilities safe to use in production workflows.

---

# 13. **Output Examples**

Both YAML and CSV examples show:

- How constraints look
    
- How descriptions appear
    
- How structure is preserved
    

This helps users understand the roundtrip behavior.

---

# 14. **Validation**

### **Roundtrip test**

Convert:

YML → CSV → YML

Compare with original.

You should see:

- Same metadata
    
- Same column descriptions
    
- Possibly different column ordering if not originally set (expected)
    

### **Summary statistics**

After conversion, scripts print counts:

`Sources: X Tables: Y Columns: Z`

This is extremely useful for QA and validation.

---

# 15. **Integration with dbt**

After generating new YAML:

`dbt parse dbt docs generate dbt docs serve`

This verifies:

- YAML is valid
    
- Descriptions appear correctly in documentation
    

---

# 16. **Git Workflow Recommendations**

- Only commit **YAML**, not CSV
    
- CSV is considered a transient editing format
    
- Recommended branch-based workflow:
    
    - Convert to CSV
        
    - Edit
        
    - Convert back
        
    - Run dbt parse
        
    - Commit YML
        

This keeps your repo clean.

---

# 17. **Performance**

The scripts have been benchmarked:

- 353 columns across 28 tables → < 1 sec conversion
    
- Low memory footprint (<50MB)
    
- Works for very large enterprise schemas
    

---

# 18. **Troubleshooting Section**

Addresses common issues:

### Data type mismatch

Case differences don’t matter.

### Column order changes

Expected if original had no order metadata.

### Constraint formatting differences

Scripts normalize and standardize format.

---

# 19. **Best Practices**

### Commit only YAML

Don’t store CSV in repo.

### Use CSV for collaboration

Business users understand spreadsheets.

### Use column_order effectively

Group fields meaningfully in documentation.

### Reserve numbers

Leave gaps for future columns.

Helpful for long-term schema evolution.

---

# 20. **File Locations**

Paths are provided so team members can find:

`dbt_sources_yml_to_csv.py dbt_sources_csv_to_yml.py DBT_SOURCES_CONVERSION_README.md`

Stored in:

`C:\claude\ScriptsProd\`

---

# 21. **Support / Version History**

- Maintainer: MacGyver
    
- Initial release: v1.0 (2025-11-21)
    
- Includes v0.7 schema support
    

This helps with version tracking and feature expectations.

---

# ✅ **Conclusion**

This documentation defines a fully production-ready system for converting dbt source YAML definitions into spreadsheet-friendly CSV format and back again.

It solves a real operational pain point: editing large YAML files and collaborating with stakeholders.

The documentation thoroughly explains:

- Why the tools exist
    
- How the tools behave
    
- What schema they follow
    
- How to validate outputs
    
- Best practices for long-term dbt documentation governance
    

Let me know if you'd like:

✔ A visual diagram of the workflow  
✔ Example output for one of your real dbt sources  
✔ Help installing or running these scripts  
✔ A code review of the scripts themselves  
✔ A custom version adapted to your Select Water workflow