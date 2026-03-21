# Windows Memory Analysis (Volatility) - Challenges

Challenges covering Windows memory analysis using the Volatility framework.

> **Reference - Basic Volatility Command Syntax:**
> ```powershell
> .\volatility_2.6_win64_standalone.exe -h | findstr /i con
> Example: .\volatility_2.6_win64_standalone.exe -f ".\cridex.vmem" --profile=WinXPSP2x86 pslist
> ```

---

## 1. What Volatility plugin is used to dump a process to an executable?

**Answer:** `procdump`

---

## 2. What Volatility plugin uses _COMMAND_HISTORY to extract command history?

**Answer:** `cmdscan`

---

## 3. What Volatility plugin is used to scan for driver objects?

**Answer:** `driverscan`

---

## 4. What Volatility plugin is used to find the memory profile of a memory image?

**Answer:** `imageinfo`

---

## 5. What switch is used to list all available Volatility plugins?

**Answer:** `--help`

---

## 6. What are the most volatile data locations on a system? (format: location,location)

**Answer:** `registers,cache`

---

## 7. What is the 12th plugin listed in the Volatility help menu?

**Answer:** `cmdscan`

---

## 8. What is the profile for the 0zapftis.vmem memory image?

Run `imageinfo` against the memory image:

```powershell
.\volatility_2.6_win64_standalone.exe -f ".\0zapftis.vmem" imageinfo
```

**Answer:** `WinXPSP2x86`

---

## 9. What command did the attacker use to check the status of the malware?

Run `cmdscan` against the memory image:

```powershell
.\volatility_2.6_win64_standalone.exe -f ".\0zapftis.vmem" --profile=WinXPSP2x86 cmdscan
```

**Answer:** `sc query malware`

---

## 10. What are the last 7 digits of the malware driver's memory offset?

Run `driverscan` and look for the entry named "malware":

```powershell
.\volatility_2.6_win64_standalone.exe -f ".\0zapftis.vmem" --profile=WinXPSP2x86 driverscan
```

The malware driver is found at offset `0x0000000001a498b8`.

**Answer:** `1a498b8`

---

## 11. What is the MD5 hash of the executable for PID 544?

Dump the process executable using `procdump`, then compute the hash:

```powershell
mkdir dump
.\volatility_2.6_win64_standalone.exe -f ".\0zapftis.vmem" --profile=WinXPSP2x86 procdump -p 544 -D dump
Get-FileHash -Algorithm MD5 .\dump\executable.544.exe
```

**Answer:** `6CEE14703054E226E87A963372F767AA`

---

## 12. What is the remote IP address and port the malware was connected to?

Run `connscan` to view network connections:

```powershell
.\volatility_2.6_win64_standalone.exe -f ".\0zapftis.vmem" --profile=WinXPSP2x86 connscan
```

**Answer:** `172.16.98.1:6666`
