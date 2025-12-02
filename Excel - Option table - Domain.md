

### 🧩 Step 1: Set up your domain (options) table in Sheet2

1. Go to **Sheet2**.
    
2. In **Column A**, enter the list of values you want for the dropdown.  
    Example:
    
    `A1: Yes A2: No`
    
3. (Optional but recommended) Give this list a **named range**:
    
    - Select the cells with your options (e.g., `A1:A2`).
        
    - In the **Name Box** (top-left of the formula bar), type a name such as `YesNoList`.
        
    - Press **Enter**.
        

---

### 📋 Step 2: Create the dropdown in Sheet1

1. Go to **Sheet1**.
    
2. Select the cell or column where you want the dropdowns (for example, column B).
    
3. Click the **Data** tab → **Data Validation**.
    
4. In the **Data Validation** dialog:
    
    - Under **Allow**, select **List**.
        
    - Under **Source**, type one of the following depending on your setup:
        
        - If you created a **named range**:
            
            `=YesNoList`
            
        - If you didn’t create a named range:
            
            `=Sheet2!A1:A2`
            
    - Check **In-cell dropdown**.
        
5. Click **OK**.
    

Now, cells in **Sheet1** will have a dropdown with “Yes” and “No” options linked to **Sheet2**.

---

### 💡 Step 3 (Optional): Make it dynamic

If you plan to add more options later in **Sheet2**, make the range dynamic:

1. Go to the **Formulas** tab → **Name Manager** → **New**.
    
2. Enter:
    
    - Name: `YesNoList`
        
    - Refers to:
        
        `=OFFSET(Sheet2!$A$1,0,0,COUNTA(Sheet2!$A:$A),1)`
        
3. Click **OK**.
    

Now the list automatically expands if you add more values below in Sheet2.