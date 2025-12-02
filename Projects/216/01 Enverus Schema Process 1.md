---
tags:
  - project
---

## Start with the Tracker
#### This shows what Schema/ FC I need to work on next is
Tracker:

[Enverus Catalog and Ingestion Tracker.xlsx](https://selectenergyservices.sharepoint.com/:x:/r/sites/Corp-DEA/_layouts/15/Doc.aspx?sourcedoc=%7BBB1399A4-BCB3-4F68-98E7-DE87DB9EA2C3%7D&file=Enverus%20Catalog%20and%20Ingestion%20Tracker.xlsx&action=default&mobileredirect=true "https://selectenergyservices.sharepoint.com/:x:/r/sites/corp-dea/_layouts/15/doc.aspx?sourcedoc=%7bbb1399a4-bcb3-4f68-98e7-de87db9ea2c3%7d&file=enverus%20catalog%20and%20ingestion%20tracker.xlsx&action=default&mobileredirect=true")
### Schemas Location 
E:\GIS\GIS Admin\Enverus Schemas

Save the Schemas either from Pro if existing in Gas and Oil database or from Shaun's SQL files Using ChatGPT:
[https://chatgpt.com/g/g-67648de158488191a2be92cfbf93c6a3-data-management-assistant](https://chatgpt.com/g/g-67648de158488191a2be92cfbf93c6a3-data-management-assistant "https://chatgpt.com/g/g-67648de158488191a2be92cfbf93c6a3-data-management-assistant")
This Chat GPT takes the DDL files from Shaun and creates schemas as JSON list, ask it to create the schema again as a table

# Templates 
The templates have been saved to %appdata%\Microsoft\Templates
This allows them to be accessed directly from Excel
- Point
- Line ( polyline in enverus)
- Polygon
- Table
These will be used if the schema cannot be exported from out EGIS database at Gasandoil admin Enverus V3
## Export Enverus Schemas from Pro
Use project 216 Enverus Navigate to folder connections, in the folder connections there is a folder that will connect to an admin version of the Postgres connection, this is needed to export a schema report for each feature class


## Enverus Developer API 
uter informationa bout each featureclass schema can be dereived from the Developer API documentation here:
[Enverus Developer API Portal](https://app.drillinginfo.com/direct/#/api/explorer/v3/activeRigs)
# Documentation and DDL SQL files 
D:\Users\OneDrive - Select Water Solutions\Enverus

# Process

1. Open project tracker find next Schema to be developed
2. open Pro, check if FC exists if so navigate to file connection for admin connection with G and O DB to export Schema report
3. Covert Schema Report to XML workspace Document
4. If the FC is not in our EGIS, find the DDL file Shaun has created and add it to the GPT above. Open the template for the FC type (point line poly, table) Start copying information over.
5. Find each length and schema attributes
6. Once Schema Repor tis filled out from DDL file, Field Summary Report, and if needed the Developer api continue with step 3, then step 7.
7. XML to FC

# Process
1. Generate Schema Report 

2. **convert schema report**
![[Converted Schema Report.png]]
3. Export XML workspace Document 
	1. Takes XML workspace and creates the empty FC **Choose Import Schema Only** 
![[Pasted image 20251029095833.png]]





# Notes


When using the Chat GPT load in the

return name, alias, field order.  sort on name ascending


