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

**Answer:**

```bash
ps --ppid 1 --no-headers | wc -l
```

```
20
```

---

## 3. What arguments were passed to the `ntpd` daemon at startup?

**Answer:**

```bash
ps aux | grep ntpd
```

Or inspect the `/proc` entry:

```bash
cat /proc/$(pgrep ntpd)/cmdline | tr '\0' ' '
```

```
-p /var/run/ntpd.pid -g -u 105:109
```

---

## 4. What is the parent process of Bombadil's Bash shell?

**Answer:**

```bash
ps -ef | grep bombadil
ps -ef | grep <bombadil_bash_ppid>
```

Trace the PPID of Bombadil's bash process:

```
sshd
```

Bombadil's bash shell was spawned by the SSH daemon.

---

## 5. What file does the 4th file descriptor (fd 3) of the `cron` daemon point to?

**Answer:**

```bash
sudo ls -l /proc/1305/fd
```

(Where 1305 is the PID of cron -- verify with `pgrep cron`)

The 4th file descriptor (fd 3) points to:

```
/run/crond.pid
```

---

## 6. What permissions does the `cron` process have on that file descriptor?

**Answer:**

```bash
sudo lsof -p $(pgrep cron) | grep crond.pid
```

The `lsof` output shows `3u` where `u` means:

```
read,write
```

The `u` flag in lsof indicates the file is open for both reading and writing.

---

## 7. Identify all orphan processes on the SysV Init machine

Orphan processes are those whose parent process has terminated, causing them to be re-parented to Init (PID 1).

**Answer:**

```
[Not completed]
```

Look for processes whose PPID is 1 but are not expected system daemons:

```bash
ps -eo pid,ppid,comm | awk '$2 == 1'
```

Compare against known services to identify true orphans.

---

## 8. Find the zombie process and identify its parent

A zombie (defunct) process has completed execution but still has an entry in the process table because its parent has not read its exit status.

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
