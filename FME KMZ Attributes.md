## 0. Quick background: KML vs KMZ

- **KML** is an XML file.
    
- **KMZ** is just a zipped KML (plus optional images/icons).
    
- Attributes for each feature (Placemark, GroundOverlay, etc.) can show up as:
    
    1. Standard KML elements (name, description) and simple ExtendedData → what you called “exposed attributes”.
        
    2. A big blob of HTML/text inside `<description>`.
        
    3. Schema-based attributes in `<ExtendedData><SchemaData>`, using a defined `<Schema>`.
        

---

## 1. “Exposed attributes”: standard tags & simple ExtendedData

### 1.1 How it looks in KML

You’ll typically see something like:

`<Placemark>   <name>Well 12-34</name>   <description>Some HTML or text...</description>   <ExtendedData>     <Data name="API">       <value>30-015-12345</value>     </Data>     <Data name="Status">       <value>ACTIVE</value>     </Data>   </ExtendedData>   <Point>     <coordinates>-104.123,32.456,0</coordinates>   </Point> </Placemark>`

Here:

- `name` is a standard, single value.
    
- `ExtendedData/Data[name]/value` pairs are simple, schema-free attributes.
    

These are usually what GIS software surfaces as normal attributes without extra work.

### 1.2 Reading in FME and converting to SHP/GDB

**FME Reader:** `KML/KMZ` reader

- FME will:
    
    - Map `<name>` to an attribute (commonly `kml_name` or `name`).
        
    - Map each `<Data name="X"><value>Y</value></Data>` to an attribute named `X` with value `Y`, _if_ ExtendedData reading is enabled (it usually is by default).
        

**To confirm and use them:**

1. Add a **KML/KMZ Reader** in Workbench pointing to your KMZ.
    
2. Run with feature caching ON and inspect a few features:
    
    - You should see attributes like `API`, `Status`, `kml_name`, etc.
        
3. Once visible as normal attributes, converting to shapefile / FGDB is straightforward:
    
    - Add a **Writer** (ESRI Shapefile or Geodatabase).
        
    - Map attributes directly (or let FME automatically carry all attributes through).
        
    - Run the workspace; the attributes you saw in the inspector will become fields in the output feature class / shapefile.
        

Usually **no extra transformers** are needed for this pattern besides optional `AttributeManager` to rename or clean field names for shapefiles.

### 1.3 In ArcGIS Pro

- Use **KML To Layer** (Conversion Tools → From KML).
    
- The output feature class typically has fields like `Name`, plus fields for each `<Data name="...">`.
    
- Save as a standalone feature class or shapefile using **Feature Class To Feature Class** if you want to move it elsewhere.
    
- No special parsing is needed for these “exposed” attributes.
    

---

## 2. Attributes inside `<description>`: HTML or text blob

This is where things get annoying.

### 2.1 How it looks in KML

Example:

`<Placemark>   <name>Well 12-34</name>   <description>     <![CDATA[       <table>         <tr><td>API</td><td>30-015-12345</td></tr>         <tr><td>Status</td><td>ACTIVE</td></tr>         <tr><td>Operator</td><td>ABC Operating LLC</td></tr>       </table>     ]]>   </description>   <Point>     <coordinates>-104.123,32.456,0</coordinates>   </Point> </Placemark>`

Here, the **only** place those attributes live is in an HTML table in the description. GIS tools will read the entire `<description>` into a single text field. You must parse it to break out API, Status, Operator, etc.

### 2.2 How FME sees it

The KML/KMZ reader will create a string attribute (e.g. `kml_description` or `description`) with the entire HTML:

`<table>   <tr><td>API</td><td>30-015-12345</td></tr>   <tr><td>Status</td><td>ACTIVE</td></tr>   ... </table>`

Nothing is split into separate attributes yet.

### 2.3 Extracting attributes in FME (recommended approach)

You have a few general approaches in FME; which one you pick depends on how structured the HTML is.

Assume the HTML is consistent: one row per attribute; first `<td>` = field name, second `<td>` = value.

#### Approach A – Regex-based parsing with StringSearcher / AttributeManager

1. **Read the KMZ** with the KML reader.
    
2. **Use `StringSearcher` (or another pattern-matching transformer)**:
    
    - Search the `kml_description` field for patterns like:
        
        - For API: `<td>API</td><td>([^<]+)</td>`
            
    - Store the captured group in a new attribute, e.g. `API`.
        
3. Repeat for each field (Status, Operator, etc.), with different regex patterns:
    
    - `<td>Status</td><td>([^<]+)</td>` → `Status`
        
    - `<td>Operator</td><td>([^<]+)</td>` → `Operator`
        
4. Optionally use `AttributeManager` afterward:
    
    - To trim whitespace.
        
    - To rename attributes for shapefile/FGDB safety (max 10 chars for shapefile).
        
5. Write to shapefile or GDB as usual.
    

This is very flexible; you simply build regex patterns that match each table row.

#### Approach B – Split table first, then parse

If the description has a more complex structure (multiple tables, line breaks, etc.), you might:

1. Use `AttributeSplitter` on `kml_description`, splitting on `<tr>` to create a list.
    
2. Loop over each entry using a **ListIterator** or similar, parsing field names and values:
    
    - For each `list{n}`, use a regex to extract `field_name` and `field_value`.
        
    - Push them into attributes (maybe via a dynamic schema if you want fully generic behaviour).
        
3. Once the attributes exist, discard the original `kml_description` or keep it as reference.
    

This approach is more work up front but more robust for messy HTML.

#### When to consider Python in FME

If the HTML is irregular or you want proper XML/HTML parsing, use a **PythonCaller** transformer:

- Feed `kml_description` into Python.
    
- Use `BeautifulSoup` or standard XML parsing to pull out key/value pairs.
    
- Set FME attributes inside the PythonCaller script.
    

This gives you very precise control at the cost of complexity.

### 2.4 In ArcGIS Pro

- `KML To Layer` → output feature class will have a field like `PopupInfo` containing the same HTML blob.
    
- To parse:
    
    - Use **Calculate Field** with Python expressions (simple string search or regex), or
        
    - Export to a table and parse externally (Python, etc.), then join back.
        
- ArcGIS Pro does not provide a “click and split description HTML into fields” tool; you will be doing the same sort of string parsing that you would do in FME, just with Python/ArcPy instead of transformers.
    

---

## 3. KML schema-based attributes: `<Schema>` and `<SchemaData>`

This is the more formal way to store attributes in KML.

### 3.1 How it looks in KML

Near the top of the KML:

`<Schema name="WellSchema" id="WellSchema">   <SimpleField name="API" type="string"/>   <SimpleField name="Status" type="string"/>   <SimpleField name="Depth" type="double"/> </Schema>`

Then for each Placemark:

`<Placemark>   <name>Well 12-34</name>   <ExtendedData>     <SchemaData schemaUrl="#WellSchema">       <SimpleData name="API">30-015-12345</SimpleData>       <SimpleData name="Status">ACTIVE</SimpleData>       <SimpleData name="Depth">8500</SimpleData>     </SchemaData>   </ExtendedData>   <Point>     <coordinates>-104.123,32.456,0</coordinates>   </Point> </Placemark>`

Here:

- The `<Schema>` defines the attribute “schema” (field names + types).
    
- Each feature’s `<SchemaData>` block provides values.
    

### 3.2 How FME sees it

The FME KML/KMZ reader is designed to understand KML schema:

- It will create attributes on the feature for each `<SimpleData>` based on `name`:
    
    - `API` = `"30-015-12345"`
        
    - `Status` = `"ACTIVE"`
        
    - `Depth` = `"8500"` (string, but you can cast to numeric if needed).
        

Normally this happens automatically; you do not need to manually parse XML. Just verify in a feature inspector that those attributes show up. If for some reason they do not:

- Check the **Reader Parameters** for the KML reader:
    
    - Look for options related to **ExtendedData** or **KML Schemas** and ensure they are turned on.
        
- Re-read and test again.
    

Once visible as attributes, the conversion is straightforward:

1. Add a Writer for shapefile / FGDB.
    
2. Connect the KML reader feature type to a writer feature type.
    
3. Run; FME will create fields `API`, `Status`, `Depth` in the output.
    

You may want to:

- Use `AttributeManager` to cast `Depth` to numeric, or specify field types in the Writer schema.
    

### 3.3 In ArcGIS Pro

- `KML To Layer` parses KML SchemaData into fields in the output feature class.
    
- Fields will be named after the `SimpleField name` (subject to Esri’s naming rules – they may be shortened or adjusted).
    
- Types may default to text; you can use **Alter Field** or **Field Calculator** to cast to numeric if needed.
    

---

## 4. Putting it together: a practical FME workflow

Here’s a typical pattern I would use for a “mystery” KMZ where attributes might be in any of the three places.

1. **Reader:**
    
    - Add `KML/KMZ Reader` pointing to the KMZ.
        
    - Make sure parameters allow ExtendedData/SchemaData to be read as attributes.
        
2. **Inspect a sample:**
    
    - Run with feature caching and open a feature in the **Visual Preview**:
        
        - Do you see attributes like `API`, `Status`, etc., already present?
            
            - Yes → they’re coming from ExtendedData or SchemaData (Pattern 1 or 3).
                
            - No → look at `kml_description` and see if the data is buried in HTML (Pattern 2).
                
3. **Branch your logic:**
    
    - **If attributes already exist:**
        
        - Optionally use `AttributeManager` to:
            
            - Clean names.
                
            - Drop unneeded KML system fields.
                
        - Write to shapefile/FGDB.
            
    - **If attributes are only in `description`:**
        
        - Use `StringSearcher` (or PythonCaller) to parse specific attributes out of `kml_description` into new attributes.
            
        - Clean them up with `AttributeManager`.
            
        - Write to shapefile/FGDB.
            
4. **Handle field naming and types for the writer:**
    
    - For shapefiles:
        
        - Ensure names ≤ 10 characters.
            
    - For FGDB:
        
        - Use the Writer schema to set field types (text/double/integer) as needed.
            
5. **(Optional) Automate in FME Flow:**
    
    - Once the logic is solid, publish the workspace to FME Flow and expose input KMZ + output location (and maybe a toggle for “parse description HTML”).

HTML to XHTML

![[Pasted image 20251211142124.png]]





XQuery Expression for XML data
To get the info inside of the  ```


```<tr> (<td> </td>) ```


![[Pasted image 20251211141642.png]]

- 1. Expression:


```declare default element namespace "http://www.w3.org/1999/xhtml";
for $x in /html/body/table/tr/td/table/tr
return fme:set-attribute($x/td[1]/text(),$x/td[2]/text())
```


- 2. Attributes to Expose 
![[Pasted image 20251211141749.png]]