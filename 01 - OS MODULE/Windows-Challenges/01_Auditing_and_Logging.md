# Windows Auditing & Logging - Challenges

Challenges covering Sysinternals tools, registry forensics, event logs, Chrome history extraction, and prefetch analysis.

---

## Question 1

**What Sysinternals tool will allow you to read the SQLite3 database containing the web history of Chrome?**

1. Sysinternals provides `strings.exe` to extract readable text from binaries and databases

**Answer:** `strings.exe`

---

## Question 2

**What is the registry location of recent docs for the current user?**

1. Open regedit or use PowerShell `Get-ItemProperty`

```cmd
reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs"
```

**Answer:** `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs`

---

## Question 3

**BAM settings are stored in different registry locations based on the version of Windows 10. What version of Windows 10 is workstation2 running? (4-digit release number)**

1. Run `winver` or query registry:

```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion" /v ReleaseId
```

**Answer:** `1803`

---

## Question 4

**Figure out the last access time of the hosts file. (Flag format: mm/dd/yyyy)**

1. Open PowerShell
2. Run:

```powershell
(Get-Item "C:\Windows\System32\drivers\etc\hosts").LastAccessTime
```

**Flag:** `Thursday, August 14, 2025 9:22:41 PM`

---

## Question 5

**What is the literal path of the prefetch directory?**

1. Default system path is constant across Windows versions

**Answer:** `C:\Windows\Prefetch`

---

## Question 6

**In the Recycle Bin, what are the first two characters of the file containing actual contents of recycled file?**

1. Navigate to `$Recycle.Bin` folder
2. Look for files starting with `$R`

**Answer:** `$R`

---

## Question 7

**In the Recycle Bin, what are the first two characters of the file containing original filename, path, file size, and deletion time?**

1. Same `$Recycle.Bin` folder
2. Look for files starting with `$I`

**Answer:** `$I`

---

## Question 8

**What are the first 8 characters of the GUID used in the UserAssist registry key (Windows 7 and later)?**

1. Registry location: `HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`
2. Note the GUID key

**Answer:** `CEBFF5CD`

---

## Question 9

**What cipher method are UserAssist files encoded in?**

1. UserAssist values are ROT13 encoded
2. Apply ROT13 to decode:

```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

**Answer:** `ROT13 (Rotate by 13)`

---

## Question 10

**What main Windows log would show invalid login attempts?**

1. Open Event Viewer > Windows Logs > Security
2. Filter on Event ID 4625 (failed logon)

**Answer:** `Security log`

---

## Question 11

**What main Windows log shows whether Windows updates were applied recently?**

1. Open Event Viewer > Windows Logs > System
2. Filter on Event IDs 19, 20, 21 (Windows Update)

**Answer:** `System log`

---

## Question 12

**When reading logs, truncated lines end with "...". What format-table switch/argument displays the full output? (Flag format: -argument)**

1. Use PowerShell Format-Table with `-Wrap`

```powershell
Get-EventLog -LogName System | Format-Table -Wrap
```

**Answer:** `-wrap`

---

## Question 13

**Find the questionable website that a user browsed to (using Chrome), that appears to be malicious.**

Machine: Workstation2 (ssh from Admin_Station)

1. Extract Chrome history DB: `C:\Users\<User>\AppData\Local\Google\Chrome\User Data\Default\History`
2. Use `strings.exe` or `sqlite3` to query `urls` table
3. Identify suspicious domain

**Flag:** `[TODO]`

---

## Question 14

**There is a file that was recently opened that may contain PII. Get the flag from the contents of the file.**

Hint: Not interested in numbers.

1. Check RecentDocs registry key or JumpLists for recently opened files
2. Open candidate file and extract contents

**Flag:** `[TODO]`

---

## Question 15

**Enter the full path of the program that was run on this computer from an abnormal location.**

1. Check Prefetch folder for unusual EXE names
2. Correlate with parent folder (abnormal location = outside Program Files/Windows)

**Flag:** `[TODO]`

---

## Question 16

**What is the creation time of the questionable file in the prefetch folder? (Flag format: mm/dd/yyyy)**

1. Open PowerShell
2. Run:

```powershell
(Get-Item "C:\Windows\Prefetch\DARK_FORCES-8F2869FC.pf").CreationTime.ToString("MM/dd/yyyy")
```

**Flag:** `02/23/2022`

---

## Question 17

**Enter the name of the questionable file in the prefetch folder.**

1. Check directory listing of `C:\Windows\Prefetch`

**Answer:** `DARK_FORCES-8F2869FC.pf`
