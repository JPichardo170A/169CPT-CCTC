# Windows PowerShell - Challenges

Challenges covering PowerShell cmdlets, file operations, string matching, and scripting.

---

## Question 1

**Windows_PowerShell_Alias**

**Answer:** `5`

---

## Question 2

**What PowerShell command lists all of the contents of a directory?**

**Answer:** `Get-ChildItem` (also `Dir` or `ls`)

---

## Question 3

**How to get the count of Get-Process properties in value:**

```powershell
(Get-Process | Select-Object -First 1 | Get-Member -MemberType Property).Count
```

---

## Question 4

**How to get the count of words in a file:**

```powershell
(Get-Content .\words2.txt).Count
```

---

## Question 5

**How to get the count for files inside the Videos (or any) folder:**

```powershell
(Get-ChildItem "C:\Users\CTF\Videos").Count
```

---

## Question 6

**Compare files and output the difference:**

```powershell
Compare-Object (Get-Content new.txt) (Get-Content old.txt)
```

---

## Question 7

**Sort and find specific line from a file:**

```powershell
(Get-Content .\words.txt | Sort-Object -Descending)[20]
```

---

## Question 8

**Count the number of unique words in words.txt:**

```powershell
(Get-Content words.txt | Sort-Object -Unique | Measure-Object).Count
```

---

## Question 9

**How many methods are available for the Get-Process cmdlet?**

Machine: Admin_Station

```powershell
(Get-Process | Get-Member -MemberType Method).Count
```

---

## Question 10

**Count the number of times, case-insensitive, "gaab" is listed in words.txt**

Note: File Location - `C:\Users\CTF\Desktop\CTF`

```powershell
(Select-String -Path words.txt -Pattern "gaab").Count
```

---

## Question 11

**Count the number of words, case-insensitive, with either "a" or "z" in a word, in words.txt:**

```powershell
(Select-String -Path words.txt -Pattern "[az]").Count
```

---

## Question 12

**Use a PowerShell loop to unzip the Omega file 1,000 times and read what is inside.**

Note: Back up the .zip file to a different directory before attempting.

```powershell
$startZip = "Omega1000.zip"
$workingDir = "Omega1000.Unzipped"
$layer = 1

if (-not (Test-Path $workingDir)) {
    New-Item -ItemType Directory -Path $workingDir | Out-Null
}

Copy-Item $startZip "$workingDir\Layer_$layer.zip"

while ($true) {
    $zipPath = "$workingDir\Layer_$layer.zip"
    $extractPath = "$workingDir\Layer_$layer"

    Expand-Archive -Path $zipPath -DestinationPath $extractPath -Force
    Write-Host "Unzipped Layer $layer"

    $nextZip = Get-ChildItem -Path $extractPath -Filter *.zip -Recurse | Select-Object -First 1

    if ($nextZip) {
        $layer++
        Copy-Item $nextZip.FullName "$workingDir\Layer_$layer.zip"
    } else {
        Write-Host "Final layer reached at Layer $layer"
        break
    }
}

$finalFiles = Get-ChildItem -Path $extractPath -File -Recurse
foreach ($file in $finalFiles) {
    Write-Host "Reading file: $($file.Name)"
    Get-Content $file.FullName
}
```

**Answer:** `Kung-Fu`

---

## Question 13

**Count the number of words in words.txt where "a" appears at least twice consecutively followed by any letter a-g.**

Example: aac... aaa...

```powershell
(Select-String -Path words.txt -Pattern "a{2,}[a-g]").Count
```

---

## Question 14

**What command would tell you if there was a profile loaded for All Users All Hosts? (Flag is the full command syntax)**

> Challenge only allows ONE attempt.

**Answer:** `Test-Path $PROFILE.AllUsersAllHosts`

---

## Question 15

**Malware is running on a PowerShell profile on the File-Server. Based on PowerShell profile order of precedence (what is read first), find the correct flag.**

The flag is the string after the #, without the preceding space.

1. Run `$Profile`
2. Run `Get-Content $Profile` - output is your flag

---

## Question 16

**To find a user by using SID:**

```powershell
Get-LocalUser | Select Name, SID
```

---

## Question 17

**Script to connect to file server:**

```powershell
New-PSDrive -Name FileServer -PSProvider FileSystem -Root "\\file-server\warrior share"
```

---

## Question 18

**Why do we care?**

- **Persistence:** Techniques adversaries use to keep access to systems across restarts, changed credentials, and other interruptions that could cut off their access
- **Privilege Escalation:** Techniques adversaries use to gain higher-level permissions on a system or network
