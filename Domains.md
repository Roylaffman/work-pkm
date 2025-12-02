---
tags:
  - gis
---
```Can we get Avery Land Services LLC to the Surveyor Domain in the Pipeline FC?

![image](blob:https://teams.microsoft.com/d2bc46f0-7137-4785-b77e-e6f481567c3a)
```
Can we get Avery Land Services LLC to the Surveyor Domain in the Pipeline FC?

![[Pasted image 20251118135501.png]]
# ✅ **How to Create a Domain in ArcGIS Pro (Species Type – Text, 255 Characters)**

### **1. Open Your Geodatabase**

Domains must be created **inside a geodatabase** (File or Enterprise).  
In the **Catalog** pane:

- Expand **Databases**
    
- Right-click the geodatabase you want
    
- Select **Domains** (or “View Domains”)
    

---

# ✅ **2. Create a New Domain**

Inside the Domains view:

1. Click **New Domain** (upper left)
    
2. Fill in the fields:
    
    - **Domain Name:** `Species_Type`
        
    - **Description:** _Species found in the project area_
        
    - **Field Type:** **Text**
        
    - **Domain Type:** **Coded Value Domain**
        
    - **Split Policy:** Default
        
    - **Merge Policy:** Default
        
    - **Length:** **255**
        

---

# ✅ **3. Add the Species to the Domain**

Scroll down to the **Coded Values** area and enter each species:

|**Code**|**Description**|
|---|---|
|Gypsum milkvetch|Gypsum milkvetch|
|Gypsum wild buckwheat|Gypsum wild buckwheat|
|Kuenzler's hedgehog cactus|Kuenzler's hedgehog cactus|
|Lee's pincushion cactus|Lee's pincushion cactus|
|Scheer's beehive cactus|Scheer’s beehive cactus|
|Allred's flax|Allred’s flax|

✔ _The "Code" and "Description" can be the same for text domains._  
✔ _Only coded-value domains allow dropdown lists in attribute tables._

---

# ✅ **4. Save the Domain**

Click **Save** (top left).  
Your domain is now created.

---

# ✅ **5. Assign the Domain to a Field**

If your feature class or table already exists:

1. Right-click your feature class
    
2. Go to **Design → Fields**
    
3. Find the field you want to assign the domain to (e.g., `Species_Type`)
    
4. Under the **Domain** column, select `Species_Type`
    
5. Click **Save**
    

Now when you open the attribute table, the field will show a **dropdown list** with the 6 species options.

---

If you want, I can also help you:

- Create a **coded-value table** for bulk importing domains
    
- Build a **lookup table** if you want scientific names included
    
- Add **group layers** for endangered species mapping in NM/Permian


# Moving to a new DC Postgresql

## ✅ **How to Move Domains to Another Geodatabase With the Feature Class (ArcGIS Pro)**

Domains are **stored at the geodatabase level**, not inside the feature class.  
So when you move a feature class to another geodatabase, the domain does **NOT** automatically transfer unless you migrate it manually.

Here’s the correct workflow:

---

### 🥇 **Method 1 — Use “Export → XML Workspace Document” (BEST METHOD)**

This moves BOTH:

- Feature class
    
- Domains
    
- Subtypes
    
- Field properties
    
- Relationship classes (if any)
    

#### **Steps**

1. **Right-click the feature dataset or feature class** in the Catalog pane.
    
2. Choose:  
    **Export → XML Workspace Document**
    
3. In the Export dialog:
    
    - Set **Export Type = Schema Only** _(recommended so you don’t migrate data unless needed)_
        
    - Check **Domains** (ArcGIS Pro includes them automatically when exporting schema)
        
4. Save the XML file.
    
5. Now go to the **target geodatabase**.
    
6. Right-click the geodatabase → **Import → XML Workspace Document**
    
7. Select the XML file.
    
8. Choose whether to import:
    
    - **Schema Only** (best when moving structure + domains)
        
    - **Schema and Data** (if you want to bring features with it)
        

✔ **Result:**  
Your **Species_Type domain** and any other domains linked to your feature class will now exist inside the new geodatabase **automatically**, and the field’s domain assignment is preserved.