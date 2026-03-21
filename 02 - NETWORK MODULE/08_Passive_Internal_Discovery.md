# Passive Internal Discovery

## DNS Configuration -- Resolver Settings & Cache

**Windows:**
```
ipconfig /displaydns
```
Displays cached DNS entries from recent queries.

**Linux:**
```bash
cat /etc/resolv.conf
```
Shows configured DNS servers used for name resolution.

**Purpose:** Understand how the system resolves domain names internally.

---

## ARP Cache -- Local IP-to-MAC Mapping

**Windows:**
```
arp -a
```
Displays current ARP table mapping IPs to MAC addresses.

**Linux:**
```bash
ip neighbor
```
Modern replacement for `arp -a` (deprecated).

**Purpose:** Identify devices on the local network and their hardware addresses.

---

## Network Connections -- Active Sockets & Services

**Windows:**
```
netstat
```

**Linux:**
```bash
ss
```
Preferred over deprecated `netstat`.

**Purpose:** View active TCP/UDP connections, listening ports, and associated processes.

**Useful Options (`-antp`):**

| Option | Description |
|---|---|
| `a` | Show all active connections and listening ports |
| `n` | Display numeric port numbers (e.g., 22 instead of SSH) |
| `t` | Show only TCP connections |
| `u` | Show only UDP connections |
| `p` | Display process IDs and names using each socket |

---

## Command Path -- Locate Executables

```bash
# Show full path of shell commands
which nmap
# Output: /usr/bin/nmap

# Locate binary, source, and man page files
whereis ssh
# Output: ssh: /usr/bin/ssh /usr/share/man/man1/ssh.1.gz
```

**Purpose:** Identify where tools are installed and how they're accessed.

---

## Routing Table -- Network Path Awareness

**Windows:**
```
route print
```

**Linux:**
```bash
ip route
```
Preferred over deprecated `netstat -r`.

**VyOS:**
```
show ip route
```

**Purpose:** View how packets are routed across networks and subnets.

Reference: https://commandmasters.com/commands/ip-route-show-linux/

---

## File Search -- Locate Sensitive or Flagged Files

```bash
# Case-sensitive search
find / -name hint* 2> /dev/null

# Case-insensitive search
find / -iname flag* 2> /dev/null
```

**Notes:**
- `-name` is case-sensitive; `-iname` is case-insensitive.
- `2> /dev/null` suppresses error output.

---

## Local Share Drive -- Check Shared Resources

Always inspect shared directories for useful files.

```bash
ls -al /usr/share/cctc
```

**Purpose:** Discover scripts, flags, or documentation placed in shared environments.

---

## SSH Configuration -- Port & Access Settings

**Windows Location:**
```
C:\Windows\System32\OpenSSH\sshd_config
```

**Linux Location:**
```bash
/etc/ssh/sshd_config
```

```bash
# View SSH port
grep Port /etc/ssh/sshd_config
```

**Purpose:** Identify SSH server settings, including port, authentication, and access controls.
