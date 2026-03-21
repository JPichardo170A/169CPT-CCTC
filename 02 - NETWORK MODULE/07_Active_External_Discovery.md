# Active External Discovery

## Methods Used for Active External Discovery

**Scanning Nature: Active** -- Direct interaction with external systems to identify assets and vulnerabilities. Techniques generate traffic and probe targets, which may be detectable.

**Common Methods:**
- Port Scanning (e.g., Nmap, Netcat)
- Banner Grabbing (e.g., Curl, Telnet)
- Service Enumeration
- OS Fingerprinting
- Web Crawling
- External Ping Sweeps

**Tools:** PING, NMAP, Netcat, Curl, Wget, /dev/tcp

**Scanning Nature: Passive** -- Observes existing traffic or public data without interacting with the target.

**Common Methods:**
- OSINT (Open-Source Intelligence)
- DNS Queries (DIG, WHOIS, Zone Transfers)
- Shodan Searches
- Packet Sniffing
- Historical Content Analysis
- Social Tactics (e.g., LinkedIn, job postings, PDF metadata)

---

## Scanning Strategy -- Directional Recon Models

| Strategy | Description |
|---|---|
| Remote to Local | Scanning external systems from a remote location targeting internal assets |
| Local to Remote | Scanning external targets from within a local network or host |
| Local to Local | Internal scanning within the same network or system |
| Remote to Remote | External scanning between two remote systems or cloud-based environments |

---

## Scanning Approach -- Tactical Execution Models

### Aim
- **Wide Range Target Scan** -- Broad sweep across multiple IPs or domains to discover unknown assets.
- **Target Specific Scan** -- Focused scan on known assets for detailed enumeration.

### Method
- **Single Source Scan** -- One scanner targeting one or multiple systems (1-to-1 or 1-to-Many).
- **Distributed Scan** -- Multiple scanners targeting one or many systems (Many-to-One or Many-to-Many).

---

## Vertical Scan -- Single Target, Multiple Ports

- **Definition:** Scanning multiple ports (some or all) on a single target IP or domain.
- **Purpose:** Identify which services are running on a specific host.
- **Use Case:** Penetration testing, asset profiling, vulnerability assessment.

```bash
# Top 100 ports
nmap scanme.nmap.org

# All ports
nmap -p- scanme.nmap.org

# Specific ports
nmap -p 22,80,443 scanme.nmap.org

# Port range
nmap -p 1-1024 scanme.nmap.org

# Service info
nmap -sV scanme.nmap.org

# Stealth SYN scan
nmap -sS scanme.nmap.org
```

**Notes:**
- May trigger IDS/IPS alerts
- Use timing options (`-T0` to `-T5`) to adjust speed and stealth
- Combine with OS detection (`-O`) for deeper profiling

---

## Horizontal Scanning Techniques

### Horizontal Scan
Scan a single (or set of) port(s) across a range of targets.

```bash
nmap -p 22 192.168.1.0/24
```

### Strobe Scan
Scan a predefined subset of ports across multiple targets.

```bash
nmap -p 21,22,23,80,443 192.168.1.0/24
```

### Block Scan
Scan all (or a range of) ports across multiple targets.

```bash
nmap -p- 192.168.1.0/24
```

---

## Distributed Scanning Techniques

Coordinated scanning from multiple sources:
- **Block Scan** -- Full port range across targets from multiple scanners.
- **Strobe Scan** -- Common ports across targets from multiple scanners.

---

## Ping Techniques -- Host Discovery

```bash
# Ping one IP
ping 172.16.82.106 -c 1

# Ping range (sweep across subnet)
for i in {1..254}; do (ping -c 1 172.16.82.$i | grep "bytes from" &) ; done
```

---

## Nmap Defaults -- Scan Behavior & Port Lists

**Default Scan Types:**
- User: TCP Full Connect Scan (`-sT`)
- Root: TCP SYN Scan (`-sS`)

**Default Ports:**
- Scans top 1000 most commonly used TCP/UDP ports.

```bash
# View TCP list
grep /tcp /usr/share/nmap/nmap-services | sort -r -k3 | less

# View UDP list
grep /udp /usr/share/nmap/nmap-services | sort -r -k3 | less
```

---

## Nmap Scan Types -- Discovery & Stealth Techniques

### Broadcast Ping / Ping Sweep
- Flags: `-sP`, `-PE`
- Purpose: Identify live hosts before scanning ports.

```bash
nmap -sP 192.168.1.0/24
```

### SYN Scan
- Flag: `-sS`
- Stealthy "half-open" TCP scan. Sends SYN, waits for SYN-ACK, then resets. Requires root.

```bash
nmap -sS scanme.nmap.org
```

### Full Connect Scan
- Flag: `-sT`
- Completes full TCP handshake. Easier to detect; does not require root.

```bash
nmap -sT scanme.nmap.org
```

### Null Scan
- Flag: `-sN`
- Sends packets with no TCP flags set. Useful for firewall evasion.

```bash
nmap -sN scanme.nmap.org
```

### FIN Scan
- Flag: `-sF`
- Sends TCP FIN packets to probe closed ports.

```bash
nmap -sF scanme.nmap.org
```

### XMAS Tree Scan
- Flag: `-sX`
- Sends packets with FIN, URG, and PSH flags set.

```bash
nmap -sX scanme.nmap.org
```

### UDP Scan
- Flag: `-sU`
- Probes UDP ports for open services. Slower and less reliable than TCP scans.

```bash
nmap -sU scanme.nmap.org
```

### Idle Scan
- Flag: `-sI`
- Uses a "zombie" host to perform stealth scans.

```bash
nmap -sI zombie_host scanme.nmap.org
```

---

## Traceroute & Firewalking -- Path Mapping & Firewall Analysis

### Traceroute

Maps the path packets take to reach a target host by incrementing TTL values.

```bash
# Basic trace
traceroute 172.16.82.106

# Custom port
traceroute 172.16.82.106 -p 123

# ICMP mode
sudo traceroute 172.16.82.106 -I

# TCP mode
sudo traceroute 172.16.82.106 -T

# TCP with port
sudo traceroute 172.16.82.106 -T -p 443
```

### Firewalking

Advanced technique to determine which ports/protocols a firewall allows through.

**Method:**
- Uses traceroute to identify gateway hop count.
- Sends packets with TTL one greater than the gateway to test port access.
- If ICMP Time Exceeded is received, the port is likely allowed.
- If no response, the port may be blocked.

```bash
# Firewalk tool
firewalk -S 80 -D 172.16.82.106 -n -p TCP

# Nmap alternative
nmap --script=firewalk --traceroute 172.16.82.106
```

Reference: https://linux.die.net/man/8/firewalk

---

## Netcat -- Banner Grabbing & Port Probing

```bash
# TCP scan
nc 172.16.82.106 22

# UDP scan
nc -u 172.16.82.106 53
```

Syntax: `nc [Target IP] [Target Port]`

The `-u` flag switches to UDP mode. Useful for manual banner grabbing and service interaction.

---

## Curl -- ASCII Output & File Transfers

```bash
# Basic HTTP
curl http://172.16.82.106

# Save output
curl http://172.16.82.106 -o output.txt

# FTP access
curl ftp://172.16.82.106

# FTP with authentication
curl ftp://172.16.82.106 -u student:password

# FTP file download
curl ftp://172.16.82.106/file.txt -o output.txt
```

---

## Wget -- Recursive Downloads & FTP Access

```bash
# Basic HTTP
wget http://172.16.82.106

# Recursive HTTP
wget -r http://172.16.82.106

# Recursive FTP
wget -r ftp://172.16.82.106

# Authenticated FTP
wget -r ftp://172.16.82.106 --user=student --password=password
```

The `-r` flag enables recursive download. Ideal for offline analysis and content archiving.
