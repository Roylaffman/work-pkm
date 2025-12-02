---
tags:
  - notes
  - git
  - github
---




# 📘 Syncing Your Obsidian Vault (OneDrive) to GitHub

### Repository: `github.com/roylaffman/work-pkm`

---

## ✅ Overview

You will:

1. Prepare your Obsidian vault folder inside OneDrive.
    
2. Create a GitHub repository (`work-pkm`).
    
3. Initialize Git in your Obsidian vault folder.
    
4. Connect the local vault to GitHub.
    
5. Commit and push your notes.
    
6. Enable automatic syncing (optional but recommended).
    

---

# 1. 📁 Locate Your Obsidian Vault in OneDrive

Find your vault (example path):

`C:\Users\<YourName>\OneDrive\Documents\Obsidian\<YourVaultName>`

Keep this folder path handy — you will run Git commands inside this folder.

---

# 2. 🛠 Create the GitHub Repository

1. Go to **[https://github.com/new](https://github.com/new)**
    
2. Set:
    
    - **Repository name:** `work-pkm`
        
    - **Owner:** `roylaffman`
        
    - **Visibility:** Public or Private (your choice)
        

Do **not** add any files (README, .gitignore, etc.).  
Choose **"Create Repository"**.

GitHub will display your new repo’s URL:

`https://github.com/roylaffman/work-pkm.git`

---

# 3. 💻 Install Git (if not installed)

Download Git for Windows:  
https://git-scm.com/download/win

During installation, keep all defaults.

---

# 4. 🧭 Initialize Git in Your Obsidian Vault

Open **Command Prompt** or **PowerShell**.

Navigate to your vault, e.g.:

`cd "C:\Users\<YourName>\OneDrive\Documents\Obsidian\<YourVaultName>"`

Run:

`git init git add . git commit -m "Initial commit - sync Obsidian vault"`

---

# 5. 🔗 Connect Vault to GitHub

Add GitHub as the remote:

`git remote add origin https://github.com/roylaffman/work-pkm.git`

Push your vault to GitHub:

`git branch -M main git push -u origin main`

---

# 6. 🛑 Recommended: Add a .gitignore for Obsidian Junk Files

Inside your vault folder, create a `.gitignore` file:

`.obsidian/cache/ .obsidian/workspace .obsidian/workspace.json .obsidian/graph.json .DS_Store .thumbs.db`

Then run:

`git add .gitignore git commit -m "Add .gitignore for Obsidian" git push`

---

# 7. 🔄 Optional: Enable Automatic Syncing

You can automate Git commits on your PC:

### Option A — Use Obsidian Git Plugin (Easiest)

1. In Obsidian → **Settings → Community Plugins**
    
2. Install **“Obsidian Git”**
    
3. Configure:
    
    - Auto-commit interval (e.g., every 15 minutes)
        
    - Auto-push enabled
        

### Option B — Manual syncing via Git

Run these commands anytime:

`git add . git commit -m "Update notes" git push`

---

# 8. 🧪 Test Syncing

Make a small change in Obsidian → save → run:

`git add . git commit -m "Test syncing" git push`

Verify changes appear on GitHub.

---

# 9. ✔ Final Folder Structure Example

Your OneDrive/Obsidian folder should now contain:

`YourVaultName/  ├─ .obsidian/  ├─ Notes/  ├─ Templates/  ├─ .git/  ├─ .gitignore  └─ README.md (optional)`