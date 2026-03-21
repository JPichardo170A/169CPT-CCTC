# Network Map

## Red Team Network Topology -- Hierarchical Structure

```
                                    [EXTERNAL NETWORK]
                                        INTERNET
                                            |
                                    [DNS SERVER]
                                 networking-ctfd-2
                                    10.50.20.11
                                   Start Flag: TXT
                                            |
                                  [PIVOT SYSTEM]
                               red-internet-host
                                  10.50.189.76
                          Credentials: net7_student10:password10
                                            |
        +-----------------------------------+-----------------------------------+
        |                                   |                                   |
   [BOUNDARY ROUTER]              [INFRASTRUCTURE ROUTER]              [IPS/MITM ROUTER]
      RED-SCR                          RED-INFRA                          RED-IPS
   172.16.120.1                     172.16.120.2                      172.16.120.9
   vyos:password                    vyos:password                     vyos:password
        |                                   |                                   |
   [DMZ GATEWAY]                            |                          [CORE DISTRIBUTION]
 eth2: 172.16.101.30                        |                              RED-POP
        |                                   |                           172.16.120.17
   [DMZ NETWORK]                            |                           vyos:password
  172.16.101.0/27                           |                                   |
        |                                   |                   +---------------+---------------+
   [DMZ SERVER]                             |                   |                               |
 red-dmz-host-1                             |          [REDHOSTS NETWORK]              [SECONDARY DIST]
  172.16.101.2                              |         172.16.182.96/27                    RED-POP2
  TCP: 22                                   |          4 Hosts                         172.16.140.5
                                            |          Gateway: RED-POP (.126)          vyos:password
                                            |                   |                               |
                                            |   +-------+-------+-------+              [REDINTDMZ2 NETWORK]
                                            |   |       |       |       |              172.16.140.32/27
                                            |  T2-Web  T4     T5      T6              2 Hosts: 33, 35
                                            |  Server  red-   red-    red-             Gateway: RED-POP2 (.62)
                                            |  .110    host-1 host-3  host-4                    |
                                            |          .106   .114    .118            +---------+---------+
                                            |                                        |                   |
                                            |                                   T3-Challenge    red-int-dmz2-host-2
                                            |                                   172.16.140.33      172.16.140.35
                                            |                                   TCP: 22,80         TCP: 22
```

---

## Router Infrastructure

### RED-SCR (Boundary Router)

- **IP:** 172.16.120.1
- **Interfaces:**
  - eth0: 172.16.120.1/29 -> INTERNET
  - eth1: 172.16.120.10/29 -> REDNET
  - eth2: 172.16.101.30/27 -> DMZ
- **Credentials:** vyos:password
- **Protocols:** BGP + OSPF

### RED-INFRA (Infrastructure Router)

- **IP:** 172.16.120.2
- **Interfaces:**
  - eth0: 10.10.205.248/29
  - eth1: 172.16.120.0/29
- **Credentials:** vyos:password

### RED-IPS (IPS/MITM Router)

- **IP:** 172.16.120.9
- **Interfaces:**
  - eth0: 172.16.120.8/29 -> INTERNET
  - eth1: 172.16.120.18/29 -> REDNET
- **Credentials:** vyos:password

### RED-POP (Core Distribution)

- **IP:** 172.16.120.17
- **Interfaces:**
  - eth0: 172.16.120.17/29 -> INTERNET
  - eth1: 172.16.182.126/27 -> REDHOSTS (Gateway)
  - eth2: 172.16.140.6/29 -> REDINTDMZ
- **Credentials:** vyos:password

### RED-POP2 (Secondary Distribution)

- **IP:** 172.16.140.5
- **Interfaces:**
  - eth0: 172.16.140.5/29 -> REDINTDMZ
  - eth1: 172.16.140.62/27 -> REDINTDMZ2 (Gateway)
- **Credentials:** vyos:password

---

## DMZ Network (172.16.101.0/27)

### red-dmz-host-1 (172.16.101.2)

- **TCP:** 22, 6010
- **UDP:** 68, 123
- **Services:** SSH, X11, NTP
- **OS:** Debian (4.19 kernel)

---

## REDHOSTS Network (172.16.182.96/27)

### T2-Web-Server (172.16.182.110)

- **TCP:** 22, 80, 1980, 1982, 1988, 1989
- **UDP:** 53, 1984, 1989
- **Services:** nginx/1.14.2, DNS, Challenge Services
- **OS:** Debian 10 + OpenSSH 7.9
- **Special:** hint-01.png ("1980s" reference)

### T4 -- red-host-1 (172.16.182.106)

- **TCP:** 22
- **UDP:** 53
- **Services:** SSH, DNS, X11
- **OS:** Debian 4.19.0-18-cloud-amd64

### T5 -- red-host-3 (172.16.182.114)

- **TCP:** 22, 6010
- **UDP:** 68, 123
- **Services:** SSH (internal only), X11, NTP
- **OS:** Debian 10 + OpenSSH 7.9

### T6 -- red-host-4 (172.16.182.118)

- **TCP:** 22, 6010
- **UDP:** 68, 123
- **Services:** SSH, X11, NTP
- **OS:** Debian 10 + OpenSSH 7.9

---

## REDINTDMZ2 Network (172.16.140.32/27)

### T3-Challenge (172.16.140.33)

- **TCP:** 22, 80, 2305, 2800, 2828
- **UDP:** 2000, 2011, 2200, 2250, 2999
- **Services:** nginx/1.14.2, Advanced Challenge Suite
- **OS:** Debian 10 + OpenSSH 7.9
- **Special:** hint-01.png (Futurama "1999-2999" reference)

### T7 -- red-int-dmz2-host-2 (172.16.140.35)

- **TCP:** 22
- **Services:** SSH
- **OS:** Linux (Debian presumed)

---

## Red Team Reconnaissance Table

| Target ID | Hostname | IP | Device Type | Ports | OS |
|---|---|---|---|---|---|
| -- | blue-internet-host | n/a | Linux VM | UDP: 5353 | Linux |
| -- | red-internet-host | 10.50.189.76 | Linux VM | TCP: 21,22,23,80,3350,3389,6010-6015; UDP: 68,123,5353 | Linux (Debian) |
| -- | networking-ctfd-2 | 10.50.20.11 | DNS Server | TCP/UDP 53 | Linux/BIND |
| -- | RED-SCR | 172.16.120.1 | VyOS Router | TCP: 22,179 | VyOS 3.13.11-1-amd64 |
| -- | RED-INFRA | 172.16.120.2 | VyOS Router | TCP: 22,179 | VyOS |
| -- | RED-IPS | 172.16.120.9 | VyOS Router | TCP: 22,179 | VyOS 3.13.11-1-amd64 |
| -- | RED-POP | 172.16.120.17 | VyOS Router | TCP: 22 | VyOS 3.13.11-1-amd64 |
| -- | RED-POP2 | 172.16.140.5 | VyOS Router | TCP: 22 | VyOS 3.13.11-1-amd64 |
| -- | red-dmz-host-1 | 172.16.101.2 | Linux Server | TCP: 22,6010; UDP: 68,123 | Debian (4.19 kernel) |
| T2 | T2-Web-Server | 172.16.182.110 | Linux Web Srv | TCP: 22,80,1980,1982,1988,1989; UDP: 53,1984,1989 | Debian 10 + OpenSSH 7.9 |
| T3 | T3-Challenge | 172.16.140.33 | Linux Server | TCP: 22,80,2305,2800,2828; UDP: 2000,2011,2200,2250,2999 | Debian 10 + OpenSSH 7.9 |
| T4 | red-host-1 | 172.16.182.106 | Linux Server | TCP: 22; UDP: 53 | Debian 4.19.0-18 |
| T5 | red-host-3 | 172.16.182.114 | Linux Server | TCP: 22,6010; UDP: 68,123 | Debian 10 + OpenSSH 7.9 |
| T6 | red-host-4 | 172.16.182.118 | Linux Server | TCP: 22,6010; UDP: 68,123 | Debian 10 + OpenSSH 7.9 |
| T7 | red-int-dmz2-host-2 | 172.16.140.35 | Linux Server | TCP: 22 | Linux (Debian) |

---

## Network Flow

```
[EXTERNAL] -> [DNS] -> [PIVOT] -> [BOUNDARY ROUTERS] -> [CORE ROUTERS] -> [TARGET NETWORKS]
```

## Attack Path

```
Internet Entry -> DNS Flag Extraction -> Pivot Compromise -> Router Traversal -> Target Exploitation
```

---

## Access Credentials

| System | Credentials |
|---|---|
| Pivot SSH | net7_student10:password10 |
| All VyOS Routers | vyos:password |
| Start Flag | DNS TXT record at networking-ctfd-2 |

---

## Challenge Services

### T2 (1980s Ports)

| Port | Challenge |
|---|---|
| TCP 1980 | ICMP ping size switch |
| TCP 1982 | Stealth scanning strategy |
| UDP 1984 | Linux dig command path |
| TCP 1988 | UDP scan detection method |
| TCP/UDP 1989 | IP display command |

### T3 (1999-2999 Ports)

| Port | Challenge |
|---|---|
| TCP 2305 | Traceroute hop count |
| TCP 2800 | DNS NS record count |
| TCP 2828 | DTIC geographic location |
| UDP 2000 | IPv6 prefix count for AS27064 |
| UDP 2011 | DNS admin email lookup |
| UDP 2200 | SSL certificate issuer |
| UDP 2250 | MAC OUI manufacturer |
| UDP 2999 | Historical board member lookup |

---

**Total Systems:** 14 devices (6 routers, 8 servers/hosts)
**Total Questions:** 42 challenges across multiple categories
