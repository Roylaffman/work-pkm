---
tags:
  - cca
  - project
---



Here’s a pattern you can use so that:

- You export a new CCA table from ArcGIS Pro (same schema as `CCA_Update.xlsx`).
- You append/merge it into an existing “master” Excel workbook that non‑GIS users work with.
- You can rerun it whenever new contracts are added.

I’ll show you a simple, reusable script and then explain how to adapt it.

---

### Assumptions

To move forward without more back‑and‑forth, I’m assuming:

- The ArcGIS export will look like the `CCA_Update` sheet you gave me (same columns).
- You have an existing master workbook, e.g. `CCA_Master.xlsx`, with a sheet called `CCA_Master` (or whatever name you like).
- You want to:
  - **Append new records** based on `cca_id` (or another key), and
  - Avoid duplicating rows that already exist in the master.

You can change the file paths and sheet names at the top of the script.

---

### Python script to update the master workbook

Save something like this as `update_cca_master.py` and run it with Python. It uses `pandas` and `openpyxl` (both are standard/common).

```python
# Script: update_cca_master.py
# Purpose: Take a new CCA table (exported from ArcGIS Pro) and
#          merge/append it into an existing master Excel workbook.

import pandas as pd
from pathlib import Path

# ----- CONFIGURATION -----
# 1. Path to the latest ArcGIS export (input)
new_cca_path = Path("CCA_Update.xlsx")          # change as needed

# 2. Path to the master workbook (output / existing)
master_path = Path("CCA_Master.xlsx")           # change as needed

# 3. Sheet names
new_sheet_name = "CCA_Update"                   # sheet in the ArcGIS export
master_sheet_name = "CCA_Master"                # sheet in the master workbook

# 4. Column that uniquely identifies a contract (used to de-duplicate)
id_column = "cca_id"                            # change if you use a different key

# ------------------------


def load_new_data(path, sheet_name):
    """Load the latest ArcGIS export as a DataFrame."""
    df_new = pd.read_excel(path, sheet_name=sheet_name, engine="openpyxl")
    return df_new


def load_master(path, sheet_name):
    """Load the existing master. If it does not exist, return empty DataFrame."""
    if not path.exists():
        return pd.DataFrame()
    try:
        df_master = pd.read_excel(path, sheet_name=sheet_name, engine="openpyxl")
    except ValueError:
        # Sheet does not exist yet
        return pd.DataFrame()
    return df_master


def merge_to_master(df_master, df_new, id_col):
    """
    Merge/append new data into master based on an ID column.
    - If master is empty, just return new data.
    - If master exists, update existing IDs and append truly new ones.
    """
    if df_master.empty:
        return df_new.copy()

    # Ensure both have the ID column
    if id_col not in df_new.columns:
        raise KeyError("ID column " + id_col + " not found in new data.")
    if id_col not in df_master.columns:
        raise KeyError("ID column " + id_col + " not found in master data.")

    # Set index to ID for easy combine/update. This will:
    # - overwrite rows in master with new data when IDs match
    # - add new rows when IDs do not exist in master
    df_master_indexed = df_master.set_index(id_col)
    df_new_indexed = df_new.set_index(id_col)

    # combine_first keeps values in df_new when present, otherwise uses df_master
    # but we want new to override old, so we reverse the order
    combined = df_new_indexed.combine_first(df_master_indexed)

    # Reset index back to a column
    combined = combined.reset_index()

    return combined


def save_master(df_master, path, sheet_name):
    """Write the updated master DataFrame back to Excel."""
    # If workbook already exists, we overwrite only the one sheet
    if path.exists():
        # Load existing workbook, write back the updated sheet
        with pd.ExcelWriter(path, engine="openpyxl", mode="a", if_sheet_exists="replace") as writer:
            df_master.to_excel(writer, sheet_name=sheet_name, index=False)
    else:
        # Create a new workbook
        with pd.ExcelWriter(path, engine="openpyxl") as writer:
            df_master.to_excel(writer, sheet_name=sheet_name, index=False)


def main():
    # 1. Load new ArcGIS export
    df_new = load_new_data(new_cca_path, new_sheet_name)

    # 2. Load existing master workbook (if any)
    df_master = load_master(master_path, master_sheet_name)

    # 3. Merge / append based on cca_id
    df_updated = merge_to_master(df_master, df_new, id_column)

    # 4. Save back to master workbook
    save_master(df_updated, master_path, master_sheet_name)

    print("Master workbook updated at:", master_path.resolve())
    print("Number of rows in updated master:", len(df_updated))


if __name__ == "__main__":
    main()
```

---

### How you’d use this in your workflow

1. In ArcGIS Pro, export your feature class table to Excel with the same schema as the `CCA_Update` sheet.
2. Save it as something like `CCA_Update.xlsx` in a known folder.
3. Make sure `CCA_Master.xlsx` exists (or let the script create it fresh the first time).
4. Run:

```bash
python update_cca_master.py
```

5. Your non‑GIS users open `CCA_Master.xlsx` and work in the `CCA_Master` sheet.

---

### Customizations you might want

You can easily tweak the script to:

- **Use a different key**  
  Change `id_column = "cca_id"` to whatever uniquely identifies a contract.
- **Append only new rows without updating existing ones**  
  Replace `merge_to_master` with a simple append where you drop duplicates:
  ```python
  df_all = pd.concat([df_master, df_new], ignore_index=True)
  df_all = df_all.drop_duplicates(subset=[id_col], keep="last")
  ```
- **Filter columns** (if the ArcGIS export has extra fields you don’t want in the master)  
  Before merging, subset the columns:
  ```python
  keep_cols = ["cca_id", "cca_name", "cca_type", ...]  # etc.
  df_new = df_new[keep_cols]
  ```

---

If you tell me:

- The exact name of your current master workbook and sheet, and
- Whether you want to overwrite existing rows when contracts change, or only add brand‑new ones,

I can tailor the script exactly to your environment and even add some basic logging or validation (e.g., flagging missing dates, weird status values, etc.).