## Service Desk / Task Setup

1. **Check for existing SD ticket specifically about TRE permits**
    
    - Search SD for any existing request related to:
        
        - “All Permits export”
            
        - TRE permits / C-147 permits / NM permits
            
        - Reconcile TRE DV, NM Workbook, DCT using permit names
            
    - Confirm whether there is already a Permits-focused ticket separate from the existing DSP Facilities lat/longs ticket (`208025000006688033`).
        
2. **Create (or update) an SD ticket for the “All Permits” export request**
    
    - If no Permits ticket exists:
        
        - Create a new SD request summarizing:
            
            - Ask: “Obtain an ‘All Permits’ export (or equivalent) from applicable states for TRE sites.”
                
            - Context: This supports reconciling sites across TRE DV, NM Workbook, and DCT using permit (C-147/permitted) names.
                
            - Mention this thread: request originated from Nathan Askins’ question to Natalie about an “All Permits” export from the states.
                
        - Attach / paste:
            
            - The full email thread.
                
            - The link to the existing DSP Facilities lat/longs ticket for reference.
                
    - If a Permits ticket already exists:
        
        - Update that ticket with:
            
            - This latest request from Nathan.
                
            - Kim’s current C-147 list.
                
            - The reconciliation context (TRE DV, NM Workbook, DCT).
                
3. **Explicitly document scope in the SD ticket**
    
    - Clarify that the task is to:
        
        - Obtain a comprehensive permit listing (e.g., all TRE-related C-147 or other permit records) from relevant state/agency systems.
            
        - Use that permit list as the canonical “truth” to:
            
            - Confirm which sites exist.
                
            - Standardize names across TRE DV, NM Workbook, and DCT.
                
    - Note assumptions:
        
        - Use permitted name / C-147 name as primary identifier (per John and Nathan’s comments).
            
        - DCT naming rules (CamelCase/underscore, no spaces or special characters) will remain the system of record; DV and NM workbook should align to that where feasible.
            

---

## 2. Clarify Requirements with Stakeholders

4. **Confirm permit sources and coverage**
    
    - Work with Natalie and Regulatory (Kim / John) to clarify:
        
        - Which states and agencies need to be queried (e.g., NMOCD, BLM, other state regulators).
            
        - Whether the “All Permits” export should cover:
            
            - Only TRE facilities, or all Select facilities with TRE relevance.
                
            - Only C-147s, or broader permit types.
                
    - Capture this in the SD ticket as acceptance criteria.
        
5. **Validate and extend Kim’s current permit list**
    
    - From Kim’s email, note:
        
        - Sites listed (Lost Tank, Earthstone, Jesse James, Jewett, Queenie, Raptor, Howard County (Big Spring), Morita, Railway, Wrage, T-Bone, Arya).
            
        - Missing C-147 copies for T-Bone and Arya.
            
    - Add a subtask in SD:
        
        - “Confirm completeness of TRE permit list with John (per Kim’s note that John must confirm all C-147s).”
            
        - “Obtain missing C-147 copies for T-Bone and Arya (if required).”
            

---

## 3. Data & Naming Standardization Work (Downstream from the Permits Export)

6. **Define the naming standard to apply across systems**
    
    - Based on Shaun’s and Nathan’s comments:
        
        - DCT remains the naming source of truth (CamelCase/underscore, no spaces/special characters).
            
        - DV and NM Workbook should conform to DCT naming where feasible.
            
    - Create a subtask in SD to:
        
        - Document the naming standard and examples (e.g., LostTank vs “Lost Tank”, TBone vs “T-Bone”, QueeniePit vs “Queenie Pit”).
            
        - Confirm with Nathan / Shaun that this is the agreed direction.
            
7. **Plan reconciliation steps using permit names**
    
    - Add subtasks to:
        
        - Use the “All Permits” export to:
            
            - Match permits to:
                
                - TRE DV site list.
                    
                - NM Workbook site list.
                    
                - DCT site list.
                    
            - Identify:
                
                - Sites present in one system but not others.
                    
                - Name mismatches that need mapping, not renaming.
                    
        - Update or create mapping tables where necessary (e.g., “DCT Name ↔ DV Name ↔ NM Workbook Name ↔ Permit Name”).
            

---

## 4. Communication / Follow-Ups

8. **Send quick note (or SD update) to distribution list**
    
    - In SD or email, confirm:
        
        - That you’ve forwarded the request to SD and created/updated the task.
            
        - That the existing DSP Facilities lat/longs ticket is separate, unless someone confirms permits were already included in that scope.
            
        - Ask them explicitly to:
            
            - Let you know ASAP if any existing SD ticket already covers “All Permits” / TRE permits specifically, so you can consolidate.
                
9. **Schedule a short working session if needed**
    
    - Optional, but helpful:
        
        - 30-minute session with Nathan, Natalie, Kim, John, Shaun, Allie (or a subset) to:
            
            - Confirm the final “All Permits export” requirements.
                
            - Agree on ownership: who pulls from the states vs who handles DV/DCT/workbook updates.
                
            - Align on naming and reconciliation steps.

# Said workbook 

|                       |                     |                       |                                                                                                                 |
| --------------------- | ------------------- | --------------------- | --------------------------------------------------------------------------------------------------------------- |
| **NM Workbook**       | **AQV**             | **DV**                | **Notes**                                                                                                       |
| Jerrah Pond #3        | JerrahPond3         | Jerrah Pond 3         | Ancillary Pond, In workbook, but not AQV or DV, Needs to be built as SF in both, Built in AQV and DV on 6.25.25 |
| Lost Tank             | LostTank            | LostTank              | AQV and DV instances don’t have spaces in their name format while the NM workbook does                          |
| Queenie               | QueeniePit          | Queenie Pit           | AQV is the only instance with a space in between name, need to have NM heads change name in NM Workbook         |
| Jesse James           | JesseJames          | JesseJames            | AQV and DV instances don’t have spaces in their name format while the NM workbook does                          |
| Jesse James Expansion | JesseJamesExpansion | Jesse James Expansion | Needs to be built in the AQV and DV as SF site, Built in AQV and DV on 6.25.25                                  |
| Raptor                | Raptor              | Raptor                |                                                                                                                 |
| Jewett                | Jewett              | Jewett                |                                                                                                                 |
| Northcutt             | Northcutt           | Northcutt             |                                                                                                                 |
| Burrata               | Burrata             | Burrata               |                                                                                                                 |
| T-Bone                | TBone               | T-Bone                | AQV doesn't have the hyphen but the DCT and NM workbook do                                                      |
| Ribeye                | Ribeye              | Ribeye                | Needs to be built in the AQV and DV as SF site, Built in AQV and DV on 6.25.25                                  |
| Red Tank              | RedTank             | RedTank               | AQV and DV instances don’t have spaces in their name format while the NM workbook does                          |
| Tall Texan            | TallTexan           | Tall Texan            | Space in DV and workbook, no space in AQV                                                                       |
| Sam Houston           | SamHouston          | Sam Houston           | Needs to be built in the AQV and DV as SF site, Built in AQV and DV on 6.25.25                                  |
| Billy the Kid         | BillTheKid          | Billy the Kid         | Not in AQV, Build in AQV, Built in AQV on 6.25.25                                                               |
| Kid Curry             | KidCurry            | Kid Curry             | Needs to be built in the AQV and DV as SF site, Built in AQV and DV on 6.25.25                                  |
| Sundance Kid          | SundanceKid         | Sundance Kid          | Not in AQV, Build in AQV, Built in AQV on 6.25.25                                                               |
| Johnny Ringo          | JohnnyRingo         | Johnny Ringo          | Needs to be built in the AQV and DV as SF site, Built in AQV and DV on 6.25.25                                  |
| Butch Cassidy         | ButchCassidy        | Butch Cassidy         | Not in AQV, Build in AQV, Built in AQV on 6.25.25                                                               |
|                       |                     |                       |                                                                                                                 |
|                       |                     |                       |                                                                                                                 |

How would we like to proceed in further standardizing names based on the DCT to DV?