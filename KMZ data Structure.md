---
tags:
  - kmz
  - gis
---
# ✅ 1. How KML/KMZ Stores Popup Information

KML features (Points, Lines, Polygons) usually store attribute/popup information in **two possible places**:

---

## **A. `<description>` Tag (HTML/Text)**

This is the most common method.  
Google Earth, Esri tools, and many KMZ creators embed the popup content inside:

`<Placemark>   <name>My Feature</name>   <description><![CDATA[       <table>         <tr><td>Operator</td><td>ABC Energy</td></tr>         <tr><td>Diameter</td><td>12 in</td></tr>       </table>   ]]></description> </Placemark>`

### Key facts:

- It may contain **raw HTML**, including tables, line breaks, lists, etc.
    
- It is **unstructured** (not regular key/value pairs).
    
- FME and Python must **parse the HTML** to extract actual attributes.
    

---

## **B. `<ExtendedData>` Tag (Structured Fields)**

This is standardized and looks like real attributes:

`<ExtendedData>   <SchemaData schemaUrl="#pipelineSchema">     <SimpleData name="Status">Active</SimpleData>     <SimpleData name="Diameter">12</SimpleData>     <SimpleData name="Operator">ABC Energy</SimpleData>   </SchemaData> </ExtendedData>`

### Key facts:

- These are **structured attributes**.
    
- Each `<SimpleData>` element = one attribute.
    
- VERY easy to extract with FME or Python (no HTML parsing necessary).
    

---

# 🧰 2. Extracting Both in FME (Full Workflow)

FME makes this extremely easy because the **KML/KMZ Reader** already understands both `<description>` and `<ExtendedData>`.

---

## **A. Extracting KML `<description>` content in FME**

When reading a KML/KMZ file:

- The entire popup HTML will be stored in the attribute:  
    **fme_description**
    

✔ FME automatically strips CDATA  
✔ HTML remains intact

### Example workflow:

1. **KML Reader**
    
2. Use **HTMLExtractor** transformer
    
    - Input: `fme_description`
        
    - Mode: extract tables or tags into attributes
        
3. Use **AttributeManager** to clean up column names
    
4. Write to database, shapefile, geodatabase, etc.
    

### Extracting table values:

In HTMLExtractor:

- Extraction Type: **Table**
    
- Output: "Attribute Per Cell"
    
- FME will create something like:
    
    `description_table_1_col_1 = Operator description_table_1_col_2 = ABC Energy`
    
- You can pivot/clean after extraction
    

---

## **B. Extracting `<ExtendedData>` in FME**

The KML reader **automatically converts `<SimpleData>` elements into feature attributes**.

Example:

`<SimpleData name="Operator">ABC Energy</SimpleData>`

Will show up in FME as:

`Operator = "ABC Energy"`

No additional transformer required.

---

## **C. Combined Workflow in FME**

**Reader → HTMLExtractor → AttributeManager → Writer**

If both `<description>` and `<ExtendedData>` exist, FME merges them automatically onto the same feature.

---

# 🐍 3. Extracting Data Using Python (Open Source)

You can do this using:

- `fastkml` (read-only KML/KMZ)
    
- `pypngunzip` or `zipfile` (KMZ unzip)
    
- `BeautifulSoup` (parse HTML in `<description>`)
    

---

# **A. Extracting `<description>` with Python + BeautifulSoup**

### Step 1: Load KMZ → KML text

`import zipfile  kmz = zipfile.ZipFile("file.kmz", "r") kml_data = kmz.read("doc.kml").decode("utf-8")`

### Step 2: Parse features using fastkml

`from fastkml import kml  k = kml.KML() k.from_string(kml_data)  doc = next(k.features()) folder = next(doc.features()) placemarks = list(folder.features())`

### Step 3: Extract `<description>` + parse HTML

`from bs4 import BeautifulSoup  for p in placemarks:     desc = p.description  # Raw HTML string     soup = BeautifulSoup(desc, "html.parser")      # Example: extract table     table_data = []     for row in soup.find_all("tr"):         cols = [c.get_text(strip=True) for c in row.find_all("td")]         table_data.append(cols)      print(table_data)`

This gives you each `<tr>` row as key/value pairs.

---

# **B. Extracting `<ExtendedData>` in Python**

`fastkml` exposes them as a dict-like structure:

`ext = p.extended_data.elements for data_block in ext:     for simple in data_block.data:         print(simple.name, simple.value)`

Example output:

`Operator ABC Energy Diameter 12 Status Active`

💡 MUCH easier than parsing HTML.

---

# 🐍 Combined Python Script (Full Example)

`import zipfile from fastkml import kml from bs4 import BeautifulSoup  def extract_from_kmz(path):     kmz = zipfile.ZipFile(path, "r")     kml_data = kmz.read("doc.kml").decode("utf-8")      k = kml.KML()     k.from_string(kml_data)      doc = next(k.features())     placemarks = list(next(doc.features()).features())      results = []      for p in placemarks:         record = {}          # --- ExtendedData ---         if p.extended_data is not None:             for block in p.extended_data.elements:                 for sd in block.data:                     record[sd.name] = sd.value          # --- Description (HTML) ---         if p.description:             soup = BeautifulSoup(p.description, "html.parser")             for row in soup.find_all("tr"):                 cols = [c.get_text(strip=True) for c in row.find_all("td")]                 if len(cols) == 2:                     record[cols[0]] = cols[1]          results.append(record)      return results   data = extract_from_kmz("pipelines.kmz") print(data)`

---

# 📌 Summary Table

|Source|Format|Difficulty|Best Tools|
|---|---|---|---|
|**`<description>`**|HTML (tables, text)|Hard—needs parsing|FME HTMLExtractor, BeautifulSoup|
|**`<ExtendedData>`**|Structured XML (`SimpleData`)|Easy|FME Reader, fastkml|

---

# 🚀 If You Want, I Can Also Generate:

✔ A full FME workspace diagram  
✔ A Python script that exports CSV/GeoJSON  
✔ A script that converts a KMZ into a clean data table  
✔ Instructions on integrating this into your FME Flow App workflow

Just let me know!