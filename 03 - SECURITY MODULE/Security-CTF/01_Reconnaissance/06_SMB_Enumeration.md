# Reconnaissance - SMB Enumeration

## Challenge Info

- **Level:** 2
- **Points:** 5

## Prompt

Identify the host with SMB available and retrieve the associated flag.
Flag Format: Unique string of twenty random characters

---

## Reference Intelligence

- Target Subnet: `192.168.28.96/27`
- Access Method: SSH tunnel via jump box
- Credentials Used:
  - Username: `student`
  - Password: `YourTempPassword`

---

## Initial Access

```bash
proxychains ssh student@192.168.28.120 -p 4242
```

Purpose: Establishes access to internal network via grey box

---

## Tunnel Architecture

### Step 1 - Local Port Forwarding from linops to Jump Box (.118)

```bash
ssh student@10.50.11.118 -L 2222:192.168.28.120:4242
```

Forwards local port 2222 to grey box port 4242 via jump box

### Step 2 - Dynamic SOCKS Proxy from Jump Box to Grey Box

```bash
ssh student@192.168.28.120 -p 4242 -D 9050
```

Creates a SOCKS proxy tunnel for proxychains routing

### Proxychains Configuration

```
socks5 127.0.0.1 9050
```

---

## Network Reconnaissance

Scan Target: `192.168.150.224/27`

```bash
for i in {1..254} ;do (ping -c 1 192.168.150.$i | grep "bytes from" &) 2>/dev/null ;done
```

Identifies live hosts in the subnet using parallel ping sweep

**Live Hosts Identified:**
- 192.168.150.225
- 192.168.150.245

---

## SMB Port Scanning

```bash
proxychains nmap -Pn -sV -p 445,139 192.168.150.245
```

**Results:**
- Port 139/tcp: open (Microsoft Windows netbios-ssn)
- Port 445/tcp: open (Microsoft Windows 7 - 10 microsoft-ds)
- Workgroup: WORKGROUP
- Hostname: 7QZVXOXE1VJXCPR

---

## Enumeration via NSE Scripts

```bash
proxychains nmap -Pn -p 445 \
  --script smb-enum-shares,smb-ls,smb-mbenum,smb-enum-users,smb-os-discovery \
  192.168.150.245
```

**Key Output:**
- Access denied to ADMIN$, C$, IPC$ (except READ on IPC$)
- OS Discovery revealed:
  - Computer name: `7qzvXoxE1VJXcPr84wsx`
  - OS: Windows 10 Enterprise
  - Workgroup: WORKGROUP

---

## Flag Extraction

Flag Source: Computer name field from `smb-os-discovery` script

**Flag:** `7qzvXoxE1VJXcPr84wsx`

---

## Final Status

- Host Identified: 192.168.150.245
- SMB Service Confirmed
- Flag Retrieved: `7qzvXoxE1VJXcPr84wsx`

---

## Notes

- Multi-hop tunneling is essential for accessing segmented internal networks
- SOCKS proxy setup enables flexible routing for tools like proxychains
- NSE scripts provide deep enumeration of SMB services and metadata
- Flags may be embedded in system identifiers such as hostnames or share names
