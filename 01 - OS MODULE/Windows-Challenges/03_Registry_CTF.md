# Windows Registry - Capture the Flags

Challenges covering Windows registry hives, startup keys, USB forensics, and network profile analysis.

---

## 1. What Windows registry path is the Volatile Hive?

**Answer:** `HKLM\HARDWARE`

---

## 2. What registry key creates the Wow6432Node for 32-bit apps on 64-bit Windows?

**Answer:** `HKEY_LOCAL_MACHINE\SOFTWARE`

---

## 3. In what registry path are the BOOT_START drivers located?

**Answer:** `HKLM\SYSTEM\CurrentControlSet\Services`

---

## 4. What start value do BOOT_START drivers have?

**Answer:** `0x0`

---

## 5. During kernel init, what registry location has SYSTEM_START drivers?

**Answer:** `HKLM\SYSTEM\CurrentControlSet\Services`

---

## 6. SERVICE_AUTO_START start value?

**Answer:** `0x2`

---

## 7. SERVICE_DEMAND_START start value?

**Answer:** `0x3`

---

## 8. When accessing remote registry, which 2 HKEYs are accessible?

**Answer:** `HKLM` and `HKU`

---

## 9. PowerShell cmdlet to list mapped drives?

**Answer:** `Get-PSDrive`

---

## 10. Native Windows GUI tool for registry?

**Answer:** `Regedit`

---

## 11. Registry hive with all machine settings?

**Answer:** `HKEY_LOCAL_MACHINE` (HKLM)

---

## 12. Registry hive with all user settings?

**Answer:** `HKEY_USERS` (HKU)

---

## 13. Registry hive with only current user's settings?

**Answer:** `HKEY_CURRENT_USER`

---

## 14. HKCU is symlinked to what subkey?

**Steps:**

1. Get your SID:

```cmd
whoami /user
```

2. Query the corresponding HKU subkey:

```cmd
reg query "HKEY_USERS\<SID>"
```

---

## 15. PowerShell command to list subkeys and contents?

**Answer:** `Get-ChildItem`

---

## 16. PowerShell command to list only contents of a key?

**Answer:** `Get-Item`

---

## 17. Registry subkey that runs every reboot? (FULL\PATH\ALL\CAPS)

**Answer:** `HKLM\SOFTWARE\MICROSOFT\WINDOWS\CURRENTVERSION\RUN`

---

## 18. Registry subkey that runs every user logon?

**Answer:** `HKCU\SOFTWARE\MICROSOFT\WINDOWS\CURRENTVERSION\RUN`

---

## 19. RunOnce on reboot?

**Answer:** `HKLM\SOFTWARE\MICROSOFT\WINDOWS\CURRENTVERSION\RUNONCE`

---

## 20. RunOnce on user logon?

**Answer:** `HKCU\SOFTWARE\MICROSOFT\WINDOWS\CURRENTVERSION\RUNONCE`

---

## 21. Suspicious value in Run subkey?

**Steps:**

```cmd
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

**Answer:** `C:\malware.exe`

---

## 22. Suspicious value in Student's Run subkey?

**Steps:**

1. Get the Student user's SID:

```powershell
get-localuser | select name, sid
```

2. Query their Run key:

```cmd
reg query "HKEY_USERS\S-1-5-21-...-1003\Software\Microsoft\Windows\CurrentVersion\Run"
```

---

## 23. Value in RunOnce?

**Steps:**

```cmd
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce
```

**Answer:** `C:\Virus.exe`

---

## 24. Value in Student's RunOnce?

**Steps:**

```cmd
reg query "HKEY_USERS\S-1-5-21-...-1003\Software\Microsoft\Windows\CurrentVersion\RunOnce"
```

**Answer:** `C:\worm.exe`

---

## 25. USB drive manufacturer?

**Steps:**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Enum\USBSTOR"
```

**Answer:** `SanDisk`

---

## 26. Suspicious user profile from registry?

**Steps:**

```powershell
Get-ChildItem "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList" |
ForEach-Object {
    $sid = $_.PSChildName
    $profilePath = (Get-ItemProperty $_.PSPath).ProfileImagePath
    [PSCustomObject]@{ SID = $sid; ProfilePath = $profilePath }
}
```

**Answer:** `Hacker_McHackerson`

---

## 27. Suspicious wireless network from registry?

**Steps:**

1. Query network profiles:

```cmd
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Profiles"
```

2. Enumerate with PowerShell:

```powershell
Get-ChildItem "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Profiles" |
ForEach-Object {
    $profile = Get-ItemProperty $_.PSPath
    [PSCustomObject]@{ GUID = $_.PSChildName; SSID = $profile.ProfileName; LastConnected = $profile.DateLastConnected }
}
```

**Answer:** `Terror_cafe_network`
