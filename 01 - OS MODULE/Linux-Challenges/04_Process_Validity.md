# Linux Process Validity - Challenges

Challenges covering process analysis, file descriptors, orphan/zombie processes, and suspicious activity.

---

## 1. What is the PID of the SysV Init process?

**Answer:**

```bash
ps aux | head
```

The Init process is always:

```
1
```

PID 1 is the first process started by the kernel and is the ancestor of all other processes.

---

## 2. How many child processes does Init (PID 1) have?

### [Step-by-Step Breakdown]

1. Use `ps` with the `--ppid` flag to list all processes whose parent PID is 1:

```bash
ps --ppid 1 --no-headers
```

   This shows every process directly spawned by Init (PID 1). The `--no-headers` flag removes the column header line so it does not get counted.

2. Pipe the output to `wc -l` to count the number of child processes:

```bash
ps --ppid 1 --no-headers | wc -l
```

3. The output gives the total count of direct children of PID 1.

**Answer:**

```
20
```

---

## 3. What arguments were passed to the `ntpd` daemon at startup?

### [Step-by-Step Breakdown]

1. Use `ps` with wide output to see the full command line of the ntpd process:

```bash
ps -eo pid,ppid,cmd | grep ntpd
```

   Or with `aux` format:

```bash
ps aux | grep ntpd
```

2. The output will show something like:

```
ntp    1234  0.0  0.1  ... /usr/sbin/ntpd -p /var/run/ntpd.pid -g -u 105:109
```

3. Alternatively, read the command line directly from `/proc`:

```bash
cat /proc/$(pgrep ntpd)/cmdline | tr '\0' ' '
```

   The `/proc/PID/cmdline` file contains the full command line with arguments separated by null bytes. The `tr '\0' ' '` converts null separators to spaces for readability.

**Answer:**

```
-p /var/run/ntpd.pid -g -u 105:109
```

---

## 4. What is the parent process of Bombadil's Bash shell?

### [Step-by-Step Breakdown]

1. **Step 1 -- Find Bombadil's bash process and its PPID:**

```bash
ps -ef | grep bombadil
```

   This lists all processes owned by or mentioning "bombadil". Look for the `bash` process and note its **PPID** (Parent PID) column.

   Example output:

```
bombadil  2345  2300  0 10:00 pts/0  00:00:00 -bash
```

   Here the PPID is `2300`.

2. **Step 2 -- Identify the parent process by its PID:**

```bash
ps -ef | grep 2300
```

   Or more precisely:

```bash
ps -p 2300 -o pid,comm
```

   This reveals the parent process name -- in this case, `sshd`.

**Answer:**

```
sshd
```

Bombadil's bash shell was spawned by the SSH daemon.

---

## 5. What file does the 4th file descriptor (fd 3) of the `cron` daemon point to?

### [Step-by-Step Breakdown]

1. **Step 1 -- Find the PID of the cron daemon:**

```bash
pgrep cron
```

   Example output: `1305`

2. **Step 2 -- List all file descriptors for the cron process:**

```bash
sudo ls -l /proc/1305/fd
```

   The `/proc/PID/fd/` directory contains symbolic links for every open file descriptor. Each symlink is named by its file descriptor number (0, 1, 2, 3, ...) and points to the actual file or resource.

3. **Step 3 -- Examine file descriptor 3 specifically:**

```bash
sudo ls -l /proc/1305/fd/3
```

   Example output:

```
lrwx------ 1 root root 64 ... 3 -> /run/crond.pid
```

   File descriptor 3 (the 4th fd, since counting starts at 0) is a symlink to `/run/crond.pid`.

   Note: fd 0 = stdin, fd 1 = stdout, fd 2 = stderr, fd 3 = the first file opened by the process.

**Answer:**

```
/run/crond.pid
```

---

## 6. What permissions does the `cron` process have on that file descriptor?

### [Step-by-Step Breakdown]

1. Use `lsof` to examine the file descriptors of the cron process with detailed information:

```bash
sudo lsof -p $(pgrep cron) | grep crond.pid
```

2. The `lsof` output includes a column showing the file descriptor number and its mode:

```
cron  1305  root  3u  REG  8,1  5  12345  /run/crond.pid
```

3. The key is the `3u` notation:
   - `3` = file descriptor number 3
   - `u` = the file is open for **read and write** (update mode)

4. The `lsof` file descriptor mode flags are:
   - `r` = read only
   - `w` = write only
   - `u` = read and write (update)

**Answer:**

```
read,write
```

The `u` flag in lsof indicates the file is open for both reading and writing.

---

## 7. Identify all orphan processes on the SysV Init machine

Orphan processes are those whose parent process has terminated, causing them to be re-parented to Init (PID 1). When a parent process dies before its child, the kernel automatically assigns the child to PID 1 (Init), which becomes responsible for cleaning up the process when it exits.

**Flag format:** List each orphan process name separated by commas.

**Answer:**

```
[Not completed]
```

Look for processes whose PPID is 1 but are not expected system daemons:

```bash
ps -eo pid,ppid,comm | awk '$2 == 1'
```

Compare against known services to identify true orphans. Legitimate daemons (sshd, cron, rsyslogd, etc.) are expected to have PPID 1. Orphans are user-space processes that should not be direct children of Init.

---

## 8. Find the zombie process and identify its parent

A zombie (defunct) process has completed execution but still has an entry in the process table because its parent has not read its exit status via the `wait()` system call. The process is dead but its PID and exit status remain until the parent collects them.

**Flag format:** The zombie process name and its parent process name.

**Answer:**

```
[Not completed]
```

```bash
ps aux | grep -i defunct
ps -eo pid,ppid,stat,comm | grep Z
```

Identify the PPID of the zombie to find the parent process that is not calling `wait()`.

---

## 9. There is a strange open port on the system. Find the executable responsible.

An unexpected listening port may indicate a backdoor, unauthorized service, or malware. Investigating unknown ports is a key step in process validity analysis.

**Flag format:** The full path to the executable.

**Answer:**

```
[Not completed]
```

```bash
sudo netstat -tlnp
sudo ss -tlnp
```

Identify unexpected listening ports and trace back to the process and its binary:

```bash
ls -l /proc/<PID>/exe
```

---

## 10. Examine the SSH process. Follow the symlink in `/proc` to confirm the binary is legitimate.

Attackers may replace system binaries with malicious versions. Verifying that a process's `/proc/PID/exe` symlink points to the expected binary location is a basic integrity check.

**Flag format:** The full path the exe symlink points to.

**Answer:**

```
[Not completed]
```

```bash
pgrep sshd
sudo ls -l /proc/<PID>/exe
```

Verify the symlink points to the expected `/usr/sbin/sshd` binary. If it points elsewhere, the process may be compromised.

---

## 11. What process is using UDP port 123?

UDP port 123 is the well-known port for the Network Time Protocol (NTP). Verifying which process holds this port ensures the time synchronization service is legitimate.

**Flag format:** The process name.

**Answer:**

```
[Not completed]
```

```bash
sudo ss -ulnp | grep 123
sudo netstat -ulnp | grep 123
```

UDP port 123 is typically used by the NTP daemon for time synchronization.

---

## 12. The process that creates zombie processes has an open file handle to a human-readable file. What is it?

When a process creates zombies, it is not properly cleaning up its children. Investigating its open file descriptors can reveal what files it is reading or writing, which may provide clues about its purpose or origin.

**Flag format:** The full path to the human-readable file.

**Answer:**

```
[Not completed]
```

```bash
ps -eo pid,ppid,stat,comm | grep Z
sudo lsof -p <zombie_parent_PID>
```

Examine all file descriptors of the zombie's parent process and find the one pointing to a text file.

---

## 13. On Minas_Tirith: Villains are chanting dark phrases over a TCP connection. Find the process and identify what they are saying.

This scenario involves detecting covert communication over a TCP socket. A malicious process is sending or receiving data over a network connection that contains suspicious content.

**Flag format:** The phrase being transmitted.

**Answer:**

```
[Not completed]
```

```bash
sudo ss -tnp
sudo netstat -tnp
sudo lsof -i TCP
```

Identify suspicious TCP connections, find the associated process, and examine its open file descriptors or read from the socket.

---

## 14. On Terra: A process is stealing `.txt` files. Identify the malicious process.

A rogue process is accessing text files that it should not be touching. This could be data exfiltration, where a process reads sensitive documents and sends them elsewhere.

**Flag format:** The malicious process name and PID.

**Answer:**

```
[Not completed]
```

```bash
sudo lsof | grep "\.txt"
ps aux --forest
```

Look for processes with unexpected file handles open to `.txt` files, especially processes that should not be accessing user documents.

---

## 15. On Terra: After a reboot, text files are still being exfiltrated. Find the persistence mechanism and the responsible process.

If a malicious process survives reboots, it has established a persistence mechanism. Common Linux persistence techniques include cron jobs, systemd services, init scripts, rc.local entries, and shell profile modifications.

**Flag format:** The persistence mechanism path and the process name.

**Answer:**

```
[Not completed]
```

Check persistence locations:

```bash
ls /etc/init.d/
systemctl list-unit-files --state=enabled
crontab -l
ls /etc/cron.*
cat /etc/rc.local
```

Identify the mechanism that restarts the malicious process after reboot.

---

## 16. On Terra: The web server keeps crashing and disk usage is abnormally high. Find the root cause.

Disk exhaustion can cause services to crash when they cannot write logs, create temp files, or allocate swap. Identifying what is consuming disk space is critical for restoring service availability.

**Flag format:** The process or file responsible for the disk usage.

**Answer:**

```
[Not completed]
```

```bash
df -h
du -sh /var/log/*
journalctl -u apache2 --no-pager | tail -50
sudo lsof +D /var/log
```

Investigate disk usage, log rotation issues, and processes filling the disk.

---

## 17. On Terra: A user's command history is being stolen every time they log on or log off. Find the mechanism.

Shell profile scripts execute automatically during login and logout. An attacker can inject commands into these scripts to exfiltrate command history, capture credentials, or establish persistence.

**Flag format:** The file path containing the malicious mechanism.

**Answer:**

```
[Not completed]
```

Check login/logout scripts:

```bash
cat /etc/profile
cat /etc/profile.d/*.sh
cat /etc/bash.bashrc
cat ~/.bashrc
cat ~/.bash_logout
cat ~/.bash_profile
```

Look for commands that copy, exfiltrate, or redirect `.bash_history` or use the `PROMPT_COMMAND` variable to capture commands.
