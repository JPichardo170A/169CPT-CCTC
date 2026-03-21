# Active Internal Discovery

## Ping Scanning -- Host Discovery via ICMP

```bash
# Single host
ping -c 1 172.16.82.106

# Subnet sweep
for i in {1..254}; do (ping -c 1 172.16.82.$i | grep "bytes from" &) ; done

# Nmap alternative
sudo nmap -sP 172.16.82.96/27
```

**Purpose:** Identify live hosts within a subnet before deeper scanning.

---

## /dev/tcp Techniques -- Manual Scanning & Banner Grabbing

### Banner Grabbing

Connects to a port and reads service response.

```bash
exec 3<>/dev/tcp/172.16.82.106/22; echo -e "" >&3; cat <&3
```

**Purpose:** Extract metadata (e.g., SSH version) from open ports.

**Notes:**
- Works in Bash with /dev/tcp support.
- Useful for stealthy, low-footprint probing.

### Port Scanning

Iterates through ports to detect which are open.

```bash
for p in {1..1023}; do (echo >/dev/tcp/172.16.82.106/$p) >/dev/null 2>&1 && echo "$p open"; done
```

**Purpose:** Lightweight alternative to Nmap for basic port scanning.

**Notes:**
- Silent scan; no banner grabbing.
- Ideal for quick checks or restricted environments.

---

## Nmap-Based Internal Scanning -- Deep Enumeration

```bash
# Full port scan
nmap -p- 172.16.82.106

# Service detection
nmap -sV 172.16.82.106

# OS detection
nmap -O 172.16.82.106

# Aggressive scan (OS + Services + Scripts)
nmap -A 172.16.82.106
```

**Purpose:** Identify open ports, running services, and OS details for internal hosts.

**Notes:**
- May trigger IDS/IPS alerts.
- Use timing options (`-T0` to `-T5`) to adjust scan speed and stealth.

---

## Netcat -- Internal Probing & Port Checks

```bash
# Verbose connection
nc -v 172.16.82.106 80

# Port scan (no data)
nc -zv 172.16.82.106 1-1000
```

**Purpose:** Identify open ports and interact with services manually. Can be scripted for automation.

---

## Network Forensics -- Mapping & Visual Analysis

**Techniques:**
- **Diagram Devices** -- Map routers, switches, endpoints.
- **Line Types** -- Use visual cues for wired/wireless links.
- **Written Information** -- Annotate IPs, hostnames, roles.
- **Coloring** -- Differentiate device types or trust zones.
- **Groupings** -- Cluster by VLAN, subnet, or function.

**Purpose:** Build a visual and analytical understanding of internal topology.

---

## Network Recon Methodology -- Tactical Guidance

**Suggested Techniques:**
1. Begin with passive observation (logs, sniffers).
2. Use ping and /dev/tcp for host and port discovery.
3. Validate services with banner grabbing and Netcat.
4. Use Nmap for deep enumeration.
5. Document findings with diagrams and notes.
6. Correlate data to identify vulnerabilities and misconfigurations.
