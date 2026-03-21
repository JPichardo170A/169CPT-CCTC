# Windows Processes Validity - Challenges

Challenges covering process analysis, Sysinternals tools, and malware hunting on Windows systems.

---

## 1. What is the full path for the folder where 32-bit applications are redirected to on a 64-bit Windows system?

**Answer:** C:\Windows\SysWOW64

On 64-bit Windows, the WoW64 (Windows 32-bit on Windows 64-bit) subsystem redirects 32-bit applications to `C:\Windows\SysWOW64`, which contains 32-bit versions of system DLLs and executables. The `System32` folder, despite its name, contains the native 64-bit binaries.

---

## 2. What is the service that starts kernel and user mode subsystems?

**Answer:** smss.exe

The Session Manager Subsystem (`smss.exe`) is one of the first user-mode processes created during boot. It initializes the Windows subsystem by launching `csrss.exe` and `winlogon.exe` for each session, and it starts the kernel-mode and user-mode subsystem components.

---

## 3. What process runs in session 0, enforces security policy, handles logons, creates access tokens, and writes to the Security Log?

**Answer:** LSASS

The Local Security Authority Subsystem Service (`lsass.exe`) is responsible for enforcing the security policy on the system. It verifies users logging on, handles password changes, creates access tokens, and writes entries to the Windows Security Log.

---

## 4. Is spoolsv.exe a server-mode or user-mode service?

**Answer:** Server-mode service

The Print Spooler service (`spoolsv.exe`) operates as a server-mode service, managing print jobs and interacting with printer drivers. It runs in the background in Session 0 and provides printing services to all user sessions.

---

## 5. Is explorer.exe a server-mode or user-mode service?

**Answer:** User-mode service

The Windows Explorer process (`explorer.exe`) operates in user mode within the user's interactive session. It provides the graphical shell, including the desktop, taskbar, Start menu, and file browsing capabilities.

---

## 6. If you see dns.exe on a system, is it more likely a user PC or a server?

**Answer:** Server

The `dns.exe` process is the Microsoft DNS Server service. It is only present on Windows Server installations that have the DNS Server role installed, making it a strong indicator that the system is a server.

---

## 7. If you see Firefox and Office 365 on a system, is it more likely a client or server?

**Answer:** Client

Web browsers and productivity suites like Firefox and Office 365 are characteristic of client workstations. Servers typically do not have end-user applications installed, as they are dedicated to providing network services.

---

## 8. How does User-Mode request resources from the kernel?

**Answer:** System calls

User-mode applications cannot directly access hardware or kernel memory. Instead, they use system calls (syscalls) to request services from the kernel. The Windows API functions ultimately translate into system calls that transition execution from user mode to kernel mode.

---

## 9. What is it called when you copy running processes for comparison at a later date?

**Answer:** Baseline

A baseline is a snapshot of the known-good state of a system, including running processes, services, network connections, and other configuration details. It serves as a reference point for detecting anomalies or unauthorized changes.

---

## 10. What can execute any part of a process's code, including parts currently being executed by another?

**Answer:** Thread

A thread is the smallest unit of execution within a process. Multiple threads within the same process share the process's memory space and resources, allowing concurrent execution of different parts of the process's code.

---

## 11. How many priority levels are there for processes?

**Answer:** 32 (0-31)

Windows uses 32 priority levels numbered 0 through 31. Levels 1-15 are dynamic (variable) priorities used by most applications, while levels 16-31 are real-time priorities. Level 0 is reserved for the zero-page thread.

---

## 12. What server does inetinfo.exe belong to?

**Answer:** IIS

The `inetinfo.exe` process is the Internet Information Services (IIS) admin service. IIS is Microsoft's web server platform that hosts websites, FTP services, and SMTP services on Windows Server.

---

## 13. What Sysinternals tool shows you persistence locations?

**Answer:** Autoruns

Autoruns displays all programs that are configured to start automatically during system boot or user login. It covers a comprehensive list of persistence locations including registry keys, startup folders, scheduled tasks, services, drivers, and many more.

---

## 14. What Sysinternals tool allows you to investigate processes?

**Answer:** ProcExp

Process Explorer (`procexp.exe`) provides detailed information about running processes, including their DLLs, handles, performance graphs, and parent-child relationships. It is an advanced replacement for Task Manager.

---

## 15. What Sysinternals tool shows you network connections?

**Answer:** tcpVIEW

TCPView provides a real-time display of all TCP and UDP endpoints on the system, showing the owning process, local and remote addresses, and connection state. It is a graphical version of `netstat` with auto-refresh capabilities.

---

## 16. What Sysinternals tools allow you to view permissions?

**Answer:** Accesschk and AccessEnum

- **AccessChk** is a command-line tool that shows effective permissions for a specific user or group on files, directories, registry keys, and services.
- **AccessEnum** provides a graphical view of file system and registry permissions, highlighting potential security weaknesses in permission configurations.

---

## 17. What Sysinternals tool shows you handles?

**Answer:** Handle

The Handle utility (`handle.exe`) displays information about open handles for any process in the system. This is useful for determining which process has a file locked or for investigating resource usage.

---

## 18. What is the name of the default user download folder?

**Answer:** downloads

The default download location for each user is `C:\Users\<username>\Downloads`. Browsers and applications typically save downloaded files to this folder unless the user specifies an alternative location.

---

## 19. What is the default public download folder path?

**Answer:** c:\users\public\downloads

The public downloads folder at `C:\Users\Public\Downloads` is accessible to all users on the system. Files placed here are visible to every user account, making it a common location for shared downloads.

---

## 20. What Sysinternals tool shows you the service load order?

**Answer:** LoadOrder

The LoadOrder utility displays the order in which services and device drivers are loaded during system startup. This is determined by the Group value in each service's registry key and the group ordering defined in the ServiceGroupOrder registry key.

---

## 21. What is the service name for the Windows Defender Firewall?

**Answer:** MpsSvc

```powershell
Get-Service MpsSvc
```

The Microsoft Protection Service (`MpsSvc`) is the service name for Windows Defender Firewall. It manages the firewall rules and network filtering for all profiles (Domain, Private, and Public).

---

## 22. What Sysinternals tool shows you the DLLs loaded into processes?

**Answer:** ListDlls

ListDlls (`listdlls.exe`) displays all DLLs loaded into a process, including their version number, full path, and base address. It can also flag DLLs that have a different version on disk than in memory, which may indicate DLL injection.

---

## 23. (Workstation1) Malware often lives alongside its required DLLs. What DLL is in the same folder as the malware?

**Steps:**

```powershell
# Step 1: Check the default public downloads folder for suspicious executables
Get-ChildItem -Path C:\Users\Public\Downloads -Force

# Step 2: Identify the suspicious process - found scvhost.exe (note: NOT svchost.exe)
# The misspelled name is a classic malware indicator

# Step 3: List DLLs in the same directory
dir C:\Users\Public\Downloads\*.dll
```

The malware `scvhost.exe` was found in `C:\Users\Public\Downloads`. Listing DLLs in the same directory reveals the accompanying library.

**Answer:** libmingwex-0.dll

---

## 24. (Workstation1) What is the name of the annoying popup executable?

**Answer:** McAfeeFireTray.exe

This process generates persistent popup windows on the system. Despite using a name that resembles legitimate McAfee software, it is not a genuine McAfee process and should be investigated as potentially malicious.

---

## 25. (Workstation1) What sends SYN_SENT connections?

**Steps:**

```powershell
# Step 1: Open TCPView from Sysinternals to inspect network connections
# Or use PowerShell:
Get-NetTCPConnection -State SynSent

# Step 2: Identify the process generating SYN_SENT connections in tcpVIEW
```

**Answer:** McAfeeFireTray.exe

TCPView reveals that `McAfeeFireTray.exe` is repeatedly sending SYN packets (SYN_SENT state) to external addresses, which is characteristic of malware attempting to establish command-and-control connections.

---

## 26. (Workstation1) Malware in a Kerberos registry key. Find the flag.

**Steps:**

```powershell
# Step 1: Open Autoruns and navigate to the Services tab
# Look for entries that are "Not Verified" or have suspicious publishers

# Step 2: Find the Not Verified entry under Kerberos
# Autoruns shows a suspicious, unverified service registered under the Kerberos service key

# Step 3: Inspect the registry Parameters key for the malicious Kerberos entry
Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\Kerberos\Parameters"

# Step 4: Read the suspicious value from the Parameters subkey
```

**Answer:** 76aGreX5

---

## 27. (Workstation1) A malware named TotallyLegit is running. Find the binary and read its contents.

**Steps:**

```powershell
# Step 1: Locate the TotallyLegit process
Get-Process | Where-Object { $_.Name -like "*TotallyLegit*" }

# Step 2: Find the path of the binary
Get-Process -Name "TotallyLegit" | Select-Object -Property Path

# Step 3: Read the contents of the binary file
Get-Content <path_to_TotallyLegit>
```

**Answer:** GwlkK3sa

---

## 28. (Workstation1) There is a file in the McAfeeFireTray directory. What does it say?

**Steps:**

```powershell
# Step 1: Find where McAfeeFireTray.exe resides
Get-Process -Name "McAfeeFireTray" | Select-Object -Property Path

# Step 2: List all files in that directory
Get-ChildItem -Path <McAfeeFireTray_directory> -Force

# Step 3: Read the contents of the file found alongside the malware
Get-Content <file_path>
```

**Answer:** StrongBad

---

## 29. (Workstation1) What permissions does TrustedInstaller have on spoolsv.exe?

**Steps:**

```powershell
# Step 1: Get the ACL for spoolsv.exe
Get-Acl C:\Windows\System32\spoolsv.exe | Format-List

# Step 2: Look at the Access entries for NT SERVICE\TrustedInstaller
(Get-Acl C:\Windows\System32\spoolsv.exe).Access | Where-Object {
    $_.IdentityReference -like "*TrustedInstaller*"
}
```

**Answer:** read and write

---

## 30. (Workstation1) What is the handle PATH for spoolsv.exe?

**Steps:**

```powershell
# Step 1: Use Sysinternals Handle tool to find handles for spoolsv.exe
handle.exe -p spoolsv.exe

# Step 2: Filter for the PATH-related handle entry
# Or use Process Explorer to inspect handles for spoolsv.exe
```

**Answer:** C:\Windows\System32\en-US\spoolsv.exe.mui

---

## 31. (Workstation1) What is the Load Order Group for the Firewall service?

**Steps:**

```powershell
# Step 1: Use the LoadOrder Sysinternals tool to view service groups
# Or query the registry directly:
Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\MpsSvc" | Select-Object Group

# Step 2: Identify the group name assigned to MpsSvc
```

**Answer:** NetworkProvider

---

## 32. (Workstation1) What is the first DLL loaded by winlogon.exe?

**Steps:**

```powershell
# Step 1: Use Sysinternals ListDlls to view DLLs loaded by winlogon.exe
listdlls.exe winlogon.exe

# Step 2: Examine the first DLL in the output list
```

**Answer:** ntd.dll

---

## 33. (Workstation1) What is the LogAllowedConnections setting for the Public firewall profile?

**Steps:**

```powershell
# Step 1: Query the Windows Defender Firewall settings for the Public profile
netsh advfirewall show publicprofile

# Step 2: Look for the LogAllowedConnections value
Get-NetFirewallProfile -Name Public | Select-Object LogAllowed
```

**Answer:** false

---

## 34. (Workstation1) A piece of malware is opening a nonstandard port. Find it.

**Steps:**

```powershell
# Step 1: List all active network connections with associated PIDs
netstat -ano

# Step 2: Identify connections on nonstandard ports and note the PID
# Found suspicious connection on PID 2412

# Step 3: Determine the process name from the PID
tasklist /FI "PID eq 2412"

# Step 4: Investigate the process further
Get-Process -Id 2412 | Select-Object Name, Path
```

**Answer:** [pending - answer depends on the specific malware found on the workstation]

---

## 35. (Workstation1) What mechanism is opening the nonstandard port?

**Steps:**

```powershell
# Step 1: After identifying the PID from the previous challenge, investigate the process
# Step 2: Trace back to the script or binary responsible

# The process was spawned by a PowerShell script
Get-Process -Id 2412 | Select-Object Name, Path, CommandLine
```

**Answer:** legit_script.ps1

---

## 36. (Workstation1) Find the flag hidden in the script that opens the nonstandard port.

**Steps:**

```powershell
# Step 1: Locate the script identified in the previous challenge
Get-Content C:\Windows\System32\legit_script.ps1

# Step 2: Identify the Base64-encoded string in the script
# Found: ZmxhZ3tzaGVsbF9leGVjdXRpb25fOD0hfQ==

# Step 3: Decode the Base64 string
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("ZmxhZ3tzaGVsbF9leGVjdXRpb25fOD0hfQ=="))
# Output: flag{shell_execution_8=!}
```

**Answer:** N0t_L3g1T_Ammiright
