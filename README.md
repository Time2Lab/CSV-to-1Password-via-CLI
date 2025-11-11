# CSV to 1Password via CLI
A simple, cross-platform Python script that imports login credentials from a CSV file into [1Password](https://1password.com/) using the official 1Password CLI (`op`).  
It’s designed for quick migrations or bulk imports of passwords you already manage in a spreadsheet — such as exports from other password managers.

---

## ✨ Features
- ✅ Reads logins from a CSV file  
- ✅ Supports per-row vault selection  
- ✅ Creates items in 1Password as `LOGIN` entries  
- ✅ Optional **dry-run** mode — preview without making changes  
- ✅ Works on **macOS, Linux, and Windows (PowerShell)**

---

## ⚙️ Requirements
- **Python 3** (works on macOS, Linux, or Windows)  
- **1Password CLI** ([Download here](https://developer.1password.com/docs/cli/get-started/))  
- You must be signed in to 1Password in your terminal or PowerShell session:
  ```bash
  op signin
  ```
To verify your setup:
```bash
op whoami
op vault list
```

---

## 🧾 CSV Format
The script expects a CSV file with these headers:
```csv
title,username,password,location,url,vault
Email Account,user@example.com,Pass123!,Work,https://mail.example.com,Private
Bank Login,bankuser,SecurePass!,Home,https://bank.example.com,Wifey and Me
Social Media,socialuser,MyPassword!,,https://social.example.com,Private
```
### Field definitions
- **title** – name of the 1Password item  
- **username** – login username  
- **password** – login password  
- **location** – optional; added to notes  
- **url** – optional; becomes the website field  
- **vault** – required; must match an existing vault (`op vault list`)  
An example file is included: **`example-passwords.csv`**

> 🧠 Note: all other `.csv` files are ignored by Git (see `.gitignore`) so you don’t accidentally commit real passwords.

---

## 🚀 Usage
### macOS / Linux / WSL
```bash
python3 import-passwords-from-csv.py
```
### Windows PowerShell
```powershell
python import-passwords-from-csv.py
```
You’ll be prompted for:
1. **CSV filename** (press Enter to use `example-passwords.csv`)  
2. **Dry-run mode**
Example run:
```text
Enter CSV filename (default: example-passwords.csv): test-passwords.csv
Enable dry-run mode? (yes/no, default: yes): no
2025-11-10 18:41:34,318 - INFO - Created: Email Account
2025-11-10 18:41:36,072 - INFO - Created: Bank Login
2025-11-10 18:41:37,852 - INFO - Created: Social Media
```
If you choose **dry-run = yes**, the script will only print what it *would* create in JSON format — no changes are made.

---

## 🧠 How It Works
- Reads each row from your CSV  
- Builds a JSON payload matching 1Password’s expected item schema  
- If dry-run: logs the JSON output  
- If not dry-run: executes  
  ```bash
  op item create --vault "<vault>" --format json
  ```  
  and pipes the JSON directly into the command.

---

## 🧩 Example JSON (dry-run output)
```json
{
  "title": "Bank Login",
  "category": "LOGIN",
  "fields": [
    {
      "id": "username",
      "type": "STRING",
      "purpose": "USERNAME",
      "label": "username",
      "value": "bankuser"
    },
    {
      "id": "password",
      "type": "CONCEALED",
      "purpose": "PASSWORD",
      "label": "password",
      "value": "SecurePass!"
    },
    {
      "id": "notesPlain",
      "type": "STRING",
      "purpose": "NOTES",
      "label": "notesPlain",
      "value": "Location: Home"
    }
  ],
  "urls": [
    {
      "label": "website",
      "href": "https://bank.example.com"
    }
  ]
}
```

---

## 🪛 Troubleshooting
| Issue | Fix |
|-------|-----|
| `op: not signed in` | Run `op signin` before using the script |
| `Vault not found` | Run `op vault list` and make sure the vault column in your CSV matches |
| `CSV file not found` | Make sure the CSV is in the same folder or give the full path |
| `zsh: command not found: pip` | Use `python3 -m ensurepip --upgrade` to install pip |
| `Permission denied` on Windows | Try running PowerShell as Administrator or make sure file isn’t open elsewhere |

---

## 🧱 .gitignore Example
The repo includes a `.gitignore` that ensures no sensitive CSVs get pushed:
```gitignore
# Ignore all CSV files...
*.csv
# ...except the example one
!example-passwords.csv

# Python cache and env
__pycache__/
*.pyc
*.pyo
*.pyd
*.egg-info/
.env

# macOS
.DS_Store
```

---

## 🧑‍💻 Example Workflow
1. Export passwords from another manager into CSV  
2. Edit the CSV to match the required headers  
3. Confirm your 1Password vault names (`op vault list`)  
4. Run the script once with dry-run = yes  
5. If everything looks good, rerun with dry-run = no

---

## 🪪 License
MIT License — feel free to use, modify, or extend this project.
