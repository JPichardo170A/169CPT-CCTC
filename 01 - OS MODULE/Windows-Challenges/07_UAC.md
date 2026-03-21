# Windows User Account Control (UAC) - Challenges

Challenges covering UAC manifests, execution levels, and registry settings.

---

## 1. What Sysinternals tool can you use to view a file's manifest?

**Answer:** sigcheck

---

## 2. What RequestedExecutionLevel runs with the same permissions as the parent process?

**Answer:** asInvoker

---

## 3. What RequestedExecutionLevel prompts for admin credentials?

**Answer:** requireAdministrator

---

## 4. What registry key holds the UAC values?

**Answer:**

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
```

---

## 5. What is the RequestedExecutionLevel of schtasks.exe?

**Answer:** asInvoker

---

## 6. What UAC property shows if UAC is enabled, and what is its value?

**Command:**

```powershell
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System").EnableLUA
```

**Flag:** `4919`

---

## 7. What UAC property controls the admin privilege level?

**Command:**

```powershell
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" -Name ConsentPromptBehaviorAdmin
```

**Flag:** `ConsentPromptBehaviorAdmin`

---

## 8. Query the UAC registry subkey and provide the flag.

**Command:**

```powershell
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System"
```

**Flag:** `NiceJob`
