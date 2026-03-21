# Windows Services - Challenges

Challenges covering service enumeration, SIDs, and registry locations.

---

## 1. What CMD command gives you information about a service?

**Answer:**

```cmd
sc query
```

---

## 2. What CMD command lists all services?

**Answer:**

```cmd
sc query state= all
```

---

## 3. What PowerShell command lists all services?

**Answer:**

```powershell
Get-Service
```

---

## 4. What registry location stores service data?

**Answer:**

```
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services
```

---

## 5. What registry subkey holds the service DLL location?

**Answer:** Parameters

---

## 6. What is the service name of the Totally-Legit service?

**Command:**

```powershell
Get-Service | Where-Object { $_.DisplayName -eq "Totally-Legit" }
```

**Flag:** `Legit`

---

## 7. What is the SID of the Totally-Legit service (bracketed portion)?

**Command:**

```powershell
(New-Object System.Security.Principal.NTAccount("NT SERVICE\Legit")).Translate([System.Security.Principal.SecurityIdentifier]).Value
```

**Output:** `S-1-5-80-...-1182961511`

**Flag:** `1182961511`
