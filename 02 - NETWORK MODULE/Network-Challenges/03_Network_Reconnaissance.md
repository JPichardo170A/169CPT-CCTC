# Network Reconnaissance Challenges

## CTF Access Instructions

| Field | Details |
|-------|---------|
| Entry Float IP | `10.50.189.76` |
| Network Number | `N` (Provided by Instructor) |
| Student Number | `10` |
| Username | `net{N}_student10` |
| Password | `10` |

```bash
ssh -X net7_student10@10.50.189.76
```

**Notes:**
- Replace `{N}` with your assigned network number.
- Use `-X` to enable graphical applications from the remote system.
- Recommended: Use **Terminator** or multi-tab terminal to manage sessions efficiently.
- Check the CTF server for updated mission tasks and intel drops.
- Always verify your assigned network number with your instructor.
- Credentials are unique to each student and session.
- Maintain operational security when accessing remote environments.

---

## Mission Details

| Field | Details |
|-------|---------|
| Operation Name | Donovian Mapping & Reconnaissance (DMR) |
| Date | XX OCT 2023 |
| Start Time | 1300 |
| Duration | 4 hours |
| Type of Operation | Scanning & Reconnaissance |

### Objective

Aid Gorgas Cyber Forces by collecting and analyzing data from Donovian cyberspace. Build a comprehensive network map including:

- Device type
- Hostname
- Interface type
- Interface IP
- Subnet Mask CIDR
- Autonomous system number
- Routing protocol
- IP network and network name groupings
- Open ports
- Operating system type and versions

### Gathered Intelligence

- `netX_studentX:passwordX`
- `vyos:password`

### Recommended Tools

- `dig` -- DNS reconnaissance
- `nmap` -- Port and service scanning
- `nc` -- Banner grabbing and port probing
- `/dev/tcp` -- Lightweight scanning and banner grabbing

---

## Scenario Instructions -- Start Flag Discovery

**Scenario Credentials:**
- Perform DNS reconnaissance on the CTFd server.
- Locate the encoded string in a DNS TXT record.
- Decode the string (e.g., Base64) to retrieve the start flag.
- Submit decoded flag to begin challenge sequence.

**Access Flow:**

```
INTERNET_HOST
  → Red-Internet_Host: DONOVIAN Compromised Pivot
      → Red Boundary Router: DONOVIAN CYBERSPACE
```

---

## Target Hosts -- Reconnaissance Assignments

### DONOVIAN Compromised Pivot

| Field | Details |
|-------|---------|
| Hostname | `red-internet-host` |
| IP | `10.50.189.76` |
| Ports | 22 |
| Action | SSH to pivot using X11 Forwarding |

```bash
ssh -X netX_studentX:passwordX@Pivot-IP
terminator
```

### T1 -- DNS Recon

| Field | Details |
|-------|---------|
| Hostname | `networking-ctfd-2.server.vta` |
| Record Type | TXT |
| IP | UNKNOWN |
| Ports | 53 |
| Action | Interrogate DNS records |

### Red Boundary Router

| Field | Details |
|-------|---------|
| Hostname | `RED-SCR` |
| IP | `172.16.120.1` |
| Ports | 22 |
| Username | `vyos` |
| Password | `password` |
| Action | Use as start point for passive/active reconnaissance |

### T2

| Field | Details |
|-------|---------|
| Hostname | UNKNOWN |
| IP | `172.16.182.110` |
| Action | Perform active reconnaissance |

### T3

| Field | Details |
|-------|---------|
| Hostname | UNKNOWN |
| IP | `172.16.140.33` |
| Action | Perform active reconnaissance |

### T4

| Field | Details |
|-------|---------|
| Hostname | UNKNOWN |
| IP | `172.16.182.106` |
| Action | Perform active reconnaissance |

### T5

| Field | Details |
|-------|---------|
| Hostname | `red-host-3` |
| IP | `172.16.182.114` |
| Action | Perform active reconnaissance |

### T6

| Field | Details |
|-------|---------|
| Hostname | UNKNOWN |
| IP | `172.16.182.118` |
| Action | Perform active reconnaissance |

### T7

| Field | Details |
|-------|---------|
| Hostname | UNKNOWN |
| IP | `172.16.140.35` |
| Action | Perform active reconnaissance |

---

## Start Flag -- Start Flag Discovery

**Level:** N/A | **Points:** N/A

### Prompt

> Enter the DECODED flag hinted in the Mission Prompt.

**Notes:**
- Questions may appear out of order due to CTFD sorting by point value.
- Some questions will unlock additional challenges.
- Open-source research may be required for certain answers.

### Steps Taken

1. Ran `dig` command on the CTF server to query DNS TXT records.
2. Retrieved encoded TXT value: `"cmVhZHlfc2V0X3NjYW4="`
3. Decoded using Base64:

```bash
echo "cmVhZHlfc2V0X3NjYW4=" | base64 -d
```

4. Submitted decoded string as the flag for Question 1.

### Verified Answer

**Flag:** `ready_set_scan`

### Notes

- Always verify encoding formats (Base64, Hex, etc.) when working with DNS TXT records.
- Keep track of decoded flags for future correlation with mission tasks.

---

## Question 1 -- Donovian Boundary

**Level:** 1 | **Points:** 5

### Prompt

> Utilizing the intelligence already provided in the mission prompt, what is the hostname of the Red Boundary Router?
>
> Format: `student@HOSTNAME` -- Submit only the HOSTNAME portion.

### Reference Intelligence

- Red Boundary Router IP: `172.16.120.1`
- Username: `vyos`
- Password: `password`
- Access via SSH from red-internet-host

### Steps Taken

1. SSH into the compromised pivot host:

```bash
ssh -X netX_student10@10.50.189.76
```

2. From the pivot, SSH into the Red Boundary Router:

```bash
ssh vyos@172.16.120.1
```

3. Entered password when prompted: `password`
4. Ran `whoami` to confirm access level -- Output: `vyos`
5. Observed hostname in shell prompt or via VyOS CLI -- Hostname: `RED-SCR`

### Verified Answer

**Answer:** `RED-SCR`

### Notes

- This hostname identifies the Red Boundary Router within Donovian cyberspace.
- VyOS CLI will be used in subsequent challenges to extract routing intelligence.
- Refer to Section 3 in Student Resources for VyOS command syntax and examples.

---

## Question 2 -- DMZ Discovery

**Level:** 1 | **Points:** 5

### Prompt

> How many host(s) did you discover on the DMZ Net? (excluding the router)

### Steps Taken

1. SSH into pivot host:

```bash
ssh -X netX_student10@10.50.189.76
```

2. SSH into Red Boundary Router:

```bash
ssh vyos@172.16.120.1
```

3. Ran `show interfaces` to identify DMZ interface:
   - `eth2` → `172.16.101.30/27`
   - Subnet: `172.16.101.0/27`
4. Verified routing with `show ip route`
5. Returned to red-internet-host and ran host discovery:

```bash
nmap -sn 172.16.101.0/27
```

6. Identified 1 live host responding to ping sweep
7. Excluded router IP (`172.16.101.30`) from final count

### Verified Answer

**Answer:** `1`

### Notes

- DMZ subnet range: `172.16.101.1 – 172.16.101.30`
- Use `nmap -sn` for non-invasive host discovery
- Host will be documented in future mapping and service enumeration

---

## Question 3 -- DMZ IP Address

**Level:** 1 | **Points:** 5

### Prompt

> What is the IP address of the host device(s) in the DMZ network?

### Steps Taken

1. Identified DMZ subnet via VyOS CLI on Red Boundary Router:
   - Interface: `eth2`
   - Subnet: `172.16.101.0/27`
2. Returned to red-internet-host and ran host discovery:

```bash
nmap -sn 172.16.101.0/27
```

3. Verified live host response from: `172.16.101.2`
4. Confirmed this IP is not the router (`172.16.101.30`)

### Verified Answer

**Answer:** `172.16.101.2`

### Notes

- Only one host was discovered in the DMZ network.
- This IP will be used in future service enumeration and mapping tasks.

---

## Question 4 -- DMZ Well Known Ports

**Level:** 1 | **Points:** 5

### Prompt

> How many well-known open TCP ports did you discover on the device(s)?

### Steps Taken

1. Identified DMZ host IP: `172.16.101.2`
2. Ran Nmap TCP scan from red-internet-host:

```bash
nmap -p 1-1023 172.16.101.2
```

3. Observed open ports in the well-known range (1-1023)
4. Verified only one open TCP port was detected

### Verified Answer

**Answer:** `1`

### Notes

- Well-known ports range: `1-1023`
- Use `nmap -p 1-1023` for targeted scan
- Port and service will be documented in future enumeration steps

---

## Question 5 -- DMZ Port Number(s)

**Level:** 1 | **Points:** 5

### Prompt

> What well-known TCP port(s) are open on the system(s)?

### Steps Taken

1. Identified DMZ host IP: `172.16.101.2`
2. Ran targeted Nmap scan from red-internet-host:

```bash
nmap -p 1-1023 172.16.101.2
```

3. Observed open TCP port(s) in the well-known range
4. Verified service response and confirmed port status

### Verified Answer

**Answer:** `22` (SSH)

### Notes

- Port 22 is a well-known TCP port used for Secure Shell (SSH) access
- This port will be used for future service enumeration and credential testing
- No other well-known ports were detected during the scan

---

## Question 6 -- DMZ Hostname

**Level:** 1 | **Points:** 5

### Prompt

> What is the Hostname of the system(s)?
>
> Example: `student@HOSTNAME` -- Copy only the part of the name where HOSTNAME is at.

### Steps Taken

1. Identified DMZ host IP: `172.16.101.2` via ARP table on `RED-SCR`
2. Attempted SSH access from `red-internet-host` using student account:

```bash
ssh -X net7_student10@172.16.101.2
```

3. Successfully authenticated and accessed the DMZ host
4. Ran `hostname` command from inside the host
5. Output confirmed hostname: `red-dmz-host-1`

### Verified Answer

**Answer:** `red-dmz-host-1`

### Notes

- Hostname was not discoverable via banner grabbing or reverse DNS
- Direct SSH access was required to retrieve hostname
- This host is part of the DMZ segment and will be used for further enumeration

---

## Question 7 -- Donovian Man in the Middle

**Level:** 1 | **Points:** 5

### Prompt

> What is the hostname of the device directly connected to the Donovian boundary out their eth1?
>
> Example: `student@HOSTNAME` -- Copy only the name where HOSTNAME is at.

### Steps Taken

1. SSH'd into Donovian boundary router (`RED-SCR`) at `172.16.120.1`
2. Ran `show arp` to identify active host on `eth1`
3. Found IP `172.16.120.9` connected via `eth1`
4. SSH'd into `172.16.120.9` using student credentials
5. Ran `hostname` command from inside host
6. Output confirmed hostname: `RED-IPS`

### Verified Answer

**Answer:** `RED-IPS`

### Notes

- Host is directly connected to Donovian boundary via `eth1`
- VyOS system; may serve as intrusion prevention or monitoring node
- Hostname not discoverable via DNS or banner; required direct access

---

## Question 8 -- Donovian Inner Boundary

**Level:** 1 | **Points:** 5

### Prompt

> What is the hostname of the device directly connected to the system discovered in Donovian Man in the Middle, out their eth1?
>
> Example: `student@HOSTNAME` -- Copy only the name where HOSTNAME is at.

### Steps Taken

1. Identified `RED-IPS` as the system from Question 7
2. SSH'd into `RED-IPS` at `172.16.120.9`
3. Ran `show interfaces` to confirm eth1 subnet: `172.16.120.18/29`
4. Ran `show arp` to identify connected host IP: `172.16.120.17`
5. SSH'd into `172.16.120.17` using student credentials
6. Ran `hostname` command from inside host
7. Output confirmed hostname: `RED-POP`

### Verified Answer

**Answer:** `RED-POP`

### Notes

- Host is directly connected to `RED-IPS` via eth1
- Hostname confirmed via direct access
- This node may serve as a pivot deeper into the Donovian internal network

---

## Question 9 -- HOSTS Discovery

**Level:** 1 | **Points:** 5

### Prompt

> How many host(s) did you discover on the HOSTS Net? (Excluding the router)

### Steps Taken

1. SSH'd into `RED-POP` at `172.16.120.17`
2. Ran `show interfaces` to confirm HOSTS Net is on `eth1`
3. Ran `show arp` to enumerate active hosts on `eth1`
4. Counted complete ARP entries with MAC addresses and flag `C`
5. Excluded router IP (`172.16.120.18`) from count

### Verified Answer

**Answer:** `4`

### Notes

- Hosts discovered: `172.16.182.114`, `172.16.182.118`, `172.16.182.106`, `172.16.182.110`
- These hosts may be targets for enumeration, credential testing, or exploitation
- Incomplete ARP entries were excluded from the count

---

## Question 10 -- HOSTS Discovery IP

**Level:** 1 | **Points:** 10

### Prompt

> What are the host IP Address(s) on the HOSTS network?
>
> (list only the last octet separated by commas and no spaces and in order from lowest to highest)

### Steps Taken

1. SSH'd into `RED-POP` at `172.16.120.17`
2. Ran `show interfaces` to confirm HOSTS Net is on `eth1`
3. Ran `show arp` to enumerate active hosts on `eth1`
4. Extracted IPs with complete ARP entries and flag `C`
5. Parsed last octet and sorted in ascending order

### Verified Answer

**Answer:** `106,110,114,118`

### Notes

- Only complete ARP entries were included
- Router IP was excluded from the list
- These IPs will be used for host-specific enumeration and service scans

---

## Question 11 -- HOSTS Discovery T2 Ports

**Level:** 1 | **Points:** 5

### Prompt

> What well-known port(s) are open on the system?
>
> (separate ports with a comma and no space)

### Steps Taken

1. Identified correct T2 host IP: `172.16.182.110`
2. Ran TCP Nmap scan from `red-internet-host`:

```bash
sudo nmap -p 1-1023 172.16.182.110
```

3. Found open TCP ports:
   - `22` (SSH)
   - `80` (HTTP)

### Verified Answer

**Answer:** `22,80`

### Notes

- Port 22 allows remote shell access
- Port 80 may host a web interface or service
- These ports are prime candidates for enumeration and exploitation

---

## Question 12 -- HOSTS Discovery T2 Recon

**Level:** 1 | **Points:** 10

### Prompt

> Interface with the web service on the 172.16.182.110 host. The hint you find provides a suggestion on the ports above the well-known that you will need to recon. What is the range?

### Steps Taken

1. Accessed web service on T2: `http://172.16.182.110`
2. Found reference to `hint-01.png` in HTML
3. Downloaded image using:

```bash
wget -r 172.16.182.110
```

4. Viewed image locally with `eog hint-01.png`
5. Interpreted clue: "We hope you like the 1980s"
6. Ran targeted Nmap scan:

```bash
sudo nmap -p 1980-3000 172.16.182.110
```

7. Discovered open ports:
   - `1980/tcp`
   - `1982/tcp`
   - `1988/tcp`
   - `1989/tcp`
8. Determined the active recon range: `1980-1989`

### Verified Answer

**Answer:** `1980-1989`

### Notes

- These ports are above the well-known range and host custom services
- Further enumeration may reveal web apps, APIs, or command interfaces
- This range was derived directly from the hint and confirmed by scan

---

## Question 13 -- HOSTS Discovery T2 Extra Ports (UDP)

**Level:** 1 | **Points:** 10

### Prompt

> Interface with the web service on the 172.16.182.110 host. The hint you find provides a suggestion on the ports above the well-known that you will need to recon. What is the range?

### Steps Taken

1. Accessed web service on T2: `http://172.16.182.110`
2. Found reference to `hint-01.png` in HTML
3. Downloaded image using:

```bash
wget -r 172.16.182.110
```

4. Viewed image locally with `eog hint-01.png`
5. Interpreted clue: "We hope you like the 1980s"
6. Ran targeted TCP scan:

```bash
sudo nmap --open -p 1980-1989 172.16.182.110
```

7. Ran targeted UDP scan:

```bash
sudo nmap --open -sU -p 1980-1989 172.16.182.110
```

8. Confirmed open ports:
   - `1984/udp`
   - `1989/tcp` and `1989/udp`

### Verified Answer

**Answer:** `1984,1989`

### Notes

- Port 1984/udp is open and running `bb` service
- Port 1989 is open on both TCP and UDP, running `tr-rsrb-p3`
- These ports are above the well-known range and were hinted at via the image

---

## Question 14 -- HOSTS Discovery T2 Info

**Level:** 1 | **Points:** 10

### Prompt

> Answer the question on UDP port 1984.

### Steps Taken

1. Discovered UDP port `1984` open on T2 (`172.16.182.110`)
2. Interacted with the service using:

```bash
echo "hello" | netcat -u 172.16.182.110 1984
```

3. Received prompt: "What Linux command should an administrator use to determine the full file path of the dig executable?"
4. Answered with: `which dig`

### Verified Answer

**Answer:** `which dig`

### Notes

- This confirms the service is interactive and responds to UDP probes
- May be used for further enumeration or credential harvesting

---

## Question 15 -- HOSTS Discovery T2 Info 2

**Level:** 1 | **Points:** 10

### Prompt

> Answer the question on UDP port 1989.

### Steps Taken

1. Discovered UDP port `1989` open on T2 (`172.16.182.110`)
2. Interacted with the service using:

```bash
echo "hello" | netcat -u 172.16.182.110 1989
```

3. Received prompt: "Which command should an administrator use to view the IP address and CIDR notation of a Linux system running a version after Ubuntu 18.04?"
4. Answered with: `ip address`

### Verified Answer

**Answer:** `ip address`

### Notes

- `ip address` is the full command used to display IP configuration
- It shows IP addresses in CIDR notation for all interfaces
- This command is standard on Ubuntu 18.04+ and other modern distros

---

## Question 16 -- HOSTS Discovery T2 Extra Ports

**Level:** 1 | **Points:** 15

### Prompt

> What TCP ports in the range did you find that were open?
>
> (List them in order and separate the ports with a comma and no space)

### Steps Taken

1. Ran targeted TCP scan on T2:

```bash
sudo nmap --open -p 1980-1989 172.16.182.110
```

2. Identified open TCP ports:
   - `1980` (pearldoc-xact)
   - `1982` (estamp)
   - `1988` (tr-rsrb-p2)
   - `1989` (tr-rsrb-p3)

### Verified Answer

**Answer:** `1980,1982,1988,1989`

### Notes

- These ports are above the well-known range and host custom services
- May be used for further enumeration or exploitation

---

## Question 17 -- HOSTS Discovery T2 Info 3

**Level:** 1 | **Points:** 10

### Prompt

> Answer the question on TCP port 1980.

### Steps Taken

1. Discovered TCP port `1980` open on T2 (`172.16.182.110`)
2. Interacted with the service using:

```bash
echo "hello" | nc 172.16.182.110 1980
```

3. Received prompt: "A malicious user would like to send abnormally large ICMP ping packets to a target. What switch would they need to use? (Example: -x)"
4. Answered with: `-s`

### Verified Answer

**Answer:** `-s`

### Notes

- `-s` allows specification of ICMP packet size
- Commonly used in stress testing or denial-of-service simulations
- This confirms the service on port 1980 is interactive and challenge-driven

---

## Question 18 -- HOSTS Discovery T2 Info 4

**Level:** 1 | **Points:** 10

### Prompt

> Answer the question on TCP port 1982.

### Steps Taken

1. Discovered TCP port `1982` open on T2 (`172.16.182.110`)
2. Interacted with the service using:

```bash
echo "hello" | nc 172.16.182.110 1982
```

3. Received prompt: "Which scanning strategy could a malicious actor use to obscure their scanning activities from a target system, while potentially implicating the transient host in legal consequences?"
4. Answered with: `Local to Remote`

### Verified Answer

**Answer:** `Local to Remote`

### Notes

- This strategy masks the attacker's origin by routing scans through a remote host
- The remote host may be blamed or investigated if the scan is detected
- Common in stealth and proxy-based reconnaissance operations

---

## Question 19 -- HOSTS Discovery T2 Info 5

**Level:** 1 | **Points:** 10

### Prompt

> Answer the question on TCP port 1988.

### Steps Taken

1. Discovered TCP port `1988` open on T2 (`172.16.182.110`)
2. Interacted with the service using:

```bash
echo "hello" | nc 172.16.182.110 1988
```

3. Received prompt: "Which Nmap scan type (from student guide) detects open ports by checking for the absence of ICMP port unreachable messages? (specify scan type or switch -xX)"
4. Answered with: `-sU`

### Verified Answer

**Answer:** `-sU`

### Notes

- `-sU` initiates a UDP scan in Nmap
- It detects open ports by checking for the absence of ICMP Type 3 Code 3 (port unreachable) messages
- Useful for discovering UDP services that don't respond directly

---

## Question 20 -- HOSTS Discovery T2 Info 6

**Level:** 1 | **Points:** 10

### Prompt

> Answer the question on TCP port 1989.

### Steps Taken

1. Discovered TCP port `1989` open on T2 (`172.16.182.110`)
2. Interacted with the service using:

```bash
echo "hello" | nc 172.16.182.110 1989
```

3. Received prompt: "Which command is used to display the contents of the DNS client resolver cache on a Windows computer?"
4. Answered with: `ipconfig /displaydns`

### Verified Answer

**Answer:** `ipconfig /displaydns`

### Notes

- This command displays all cached DNS entries on a Windows system
- Useful for troubleshooting DNS resolution issues and verifying domain access history
- Available on all modern Windows versions

---

## Question 21 -- HOSTS Discovery T4 Ports

**Level:** 1 | **Points:** 5

### Prompt

> What well-known port(s) are open on the system?
>
> (separate ports with a comma and no space)

### Steps Taken

1. Scanned well-known ports (0-1000) on T4 (`172.16.182.106`) using:

```bash
sudo nmap -p 0-1000 172.16.182.106
```

2. Nmap results showed:
   - `22/tcp open ssh`

### Verified Answer

**Answer:** `22`

### Notes

- Port 22 is used for SSH (Secure Shell)
- Indicates remote login or command execution capability
- No other well-known ports were open

---

## Question 22 -- HOSTS Discovery T4 Hostname

**Level:** 1 | **Points:** 5

### Prompt

> What is the Hostname of the system?
>
> (Copy only the name where HOSTNAME is at)

### Steps Taken

1. Connected to T4 system at `172.16.182.106` using SSH
2. Observed shell prompt or ran `hostname` command
3. Identified hostname: `red-host-1`

### Verified Answer

**Answer:** `red-host-1`

### Notes

- Hostname confirms identity of the T4 system
- Useful for tracking and correlating system logs or network activity

---

## Question 23 -- HOSTS Discovery T5 Ports

**Level:** 1 | **Points:** 5

### Prompt

> What well-known port(s) are open on the system?
>
> (separate ports with a comma and no space)

### Steps Taken

1. Scanned well-known ports (0-1000) on T5 (`172.16.182.114`) using:

```bash
sudo nmap -p 0-1000 172.16.182.114
```

2. Nmap results showed:
   - `22/tcp open ssh`
3. Identified hostname: `red-host-3`

### Verified Answer

**Answer:** `22`

### Notes

- Port 22 is used for SSH (Secure Shell)
- Indicates remote login or command execution capability
- No other well-known ports were open

---

## Question 24 -- HOSTS Discovery T5 Hostname

**Level:** 1 | **Points:** 5

### Prompt

> What is the Hostname of the system?
>
> (Copy only the name where HOSTNAME is at)

### Steps Taken

1. Connected to T5 system at `172.16.182.114` using SSH
2. Observed shell prompt: `student@red-host-3:~$`
3. Extracted hostname: `red-host-3`

### Verified Answer

**Answer:** `red-host-3`

### Notes

- Hostname confirms identity of the T5 system
- Useful for correlating logs, network activity, and system roles

---

## Question 25 -- HOSTS Discovery T6 Ports

**Level:** 1 | **Points:** 5

### Prompt

> What well-known port(s) are open on the system?
>
> (separate ports with a comma and no space)

### Steps Taken

1. Scanned well-known ports (0-1023) on T6 (`172.16.182.118`) using:

```bash
sudo nmap -p 0-1023 172.16.182.118
```

2. Nmap results showed:
   - `22/tcp open ssh`

### Verified Answer

**Answer:** `22`

### Notes

- Port 22 is used for SSH (Secure Shell)
- Indicates remote login or command execution capability
- No other well-known ports were open

---

## Question 26 -- HOSTS Discovery T6 Hostname

**Level:** 1 | **Points:** 5

### Prompt

> What is the Hostname of the system?
>
> (Copy only the name where HOSTNAME is at)

### Steps Taken

1. SSH'd into T6 system at `172.16.182.118` using:

```bash
ssh -X net7_student10@172.16.182.118
```

2. Observed shell prompt: `net7_student10@red-host-4:~$`
3. Ran `hostname` command to confirm -- Output: `red-host-4`

### Verified Answer

**Answer:** `red-host-4`

### Notes

- Hostname confirms identity of T6 system
- Completes mapping for `172.16.182.118`

---

## Question 27 -- Donovian Past the Inner Boundary

**Level:** 1 | **Points:** 5

### Prompt

> What is the hostname of the device directly connected to the system discovered in Donovian Inner Boundary, out their eth2?
>
> (Copy only the name where HOSTNAME is at)

### Steps Taken

1. Identified `RED-POP` as the Donovian Inner Boundary system (from Q8)
2. Logged into `RED-POP` using:

```bash
ssh -X vyos@172.16.120.17
```

3. Ran `show interfaces` to confirm eth2 subnet:
   - eth2 → `172.16.140.6/29`
4. Ran `show arp` to identify connected host on eth2:
   - Resolved MAC for `172.16.140.5`
5. Pivoted from `red-internet-host` into `172.16.140.5` using:

```bash
ssh -X vyos@172.16.140.5
```

6. Ran `show host name` to confirm hostname -- Output: `RED-POP2`

### Verified Answer

**Answer:** `RED-POP2`

### Notes

- Host is directly connected to `RED-POP` via eth2
- Confirms identity of post-boundary node at `172.16.140.5`
- Uses VyOS syntax (`show host name`) instead of standard `hostname`

---

## Question 28 -- DMZ2 Discovery IP

**Level:** 1 | **Points:** 10

### Prompt

> What are the host ip address(s) in the DMZ2 network?
>
> (List only the last octet separated by commas and no spaces and in order from lowest to highest)
>
> Example: `55,90,101,231`

### Steps Taken

1. Pivoted into RED-POP2:

```bash
ssh -X vyos@172.16.140.5
```

2. Ran `show interfaces`:
   - eth1 = `172.16.140.62/27` labeled `REDINTDMZ2`
3. Ran `show arp`:
   - Confirmed MAC bindings for: `172.16.140.33` and `172.16.140.35`
4. Validated that `REDINTDMZ2` is the correct DMZ2 segment
5. Extracted last octets and sorted: `33,35`

### Verified Answer

**Answer:** `33,35`

---

## Question 29 -- DMZ2 Discovery T3 Ports

**Level:** 1 | **Points:** 5

### Prompt

> What well-known port(s) are open on the system?
>
> (Separate ports with a comma and no space)
>
> Example: `20,21,22,23,80`

### Steps Taken

1. Ran full TCP scan from red-internet-host:

```bash
nmap -Pn -p- 172.16.140.33
```

2. Nmap results:
   - `22/tcp` open (SSH)
   - `80/tcp` open (HTTP)
   - `2305/tcp` open (mt-scaleserver) -- not well-known
   - `2800/tcp` open (acc-raid) -- not well-known
   - `2828/tcp` open (itm-lm) -- not well-known
3. Filtered only well-known ports per prompt
4. Sorted and formatted answer: `22,80`

### Verified Answer

**Answer:** `22,80`

---

## Question 30 -- DMZ2 Recon T3

**Level:** 1 | **Points:** 10

### Prompt

> Interface with the web service on T3. The hint provides a suggestion on the ports above the well-known that you will need to recon. What is the range?
>
> Format: `low-high`
>
> Example: `1000-2000`

### Steps Taken

1. Ran recursive web scrape on T3:

```bash
wget -r 172.16.140.33
```

2. Retrieved:
   - `index.html` (200 OK)
   - `robots.txt` (404 Not Found)
   - `hint-01.png` (200 OK)
3. Viewed image hint:

```bash
eog http://172.16.140.33/hint-01.png
```

4. Image referenced *Futurama* character Philip J. Fry -- Cryogenically frozen from **1999 to 2999**
5. Extracted year range from hint: `1999-2999`

### Verified Answer

**Answer:** `1999-2999`

---

## Question 31 -- DMZ2 Discovery T3 Extra Ports

**Level:** 1 | **Points:** 15

### Prompt

> Which TCP ports were open in the range?
>
> Range derived from previous question: `1999-2999`
>
> Format: comma-separated, no spaces, numerical order
>
> Example: `2001,2002,2004`

### Steps Taken

1. Ran full TCP scan on T3 (`172.16.140.33`):

```bash
nmap -Pn -p- 172.16.140.33
```

2. Identified open ports:
   - `22/tcp` (SSH) -- outside range
   - `80/tcp` (HTTP) -- outside range
   - `2305/tcp` -- in range
   - `2800/tcp` -- in range
   - `2828/tcp` -- in range
3. Filtered ports within range `1999-2999`
4. Sorted numerically and formatted per prompt

### Verified Answer

**Answer:** `2305,2800,2828`

---

## Question 32 -- DMZ2 Discovery T3 Extra Ports 2

**Level:** 1 | **Points:** 15

### Prompt

> What UDP port(s) did you find that were open?
>
> Range derived from previous question: `1999-2999`
>
> Format: comma-separated, no spaces, numerical order
>
> Example: `2001,2003,2005`

### Steps Taken

1. Ran privileged UDP scan on T3 (`172.16.140.33`):

```bash
sudo nmap -sU -Pn -p1999-2999 172.16.140.33 --min-rate 5000
```

2. Identified open UDP ports:
   - `2000/udp` (cisco-sccp)
   - `2011/udp` (servserv)
   - `2200/udp` (ici)
   - `2250/udp` (remote-collab)
   - `2999/udp` (remoteware-un)
3. Sorted numerically and formatted per prompt

### Verified Answer

**Answer:** `2000,2011,2200,2250,2999`

---

## Question 33 -- DMZ2 Discovery T3 Info

**Level:** 1 | **Points:** 10

### Prompt

> Flag is the answer to the question on TCP port 2305.
>
> Service Prompt: "How many hops is it to dash.ent1.pcte.mil from your red-internet-host?"

### Steps Taken

1. Interfaced with TCP port 2305:

```bash
echo "hello" | nc 172.16.140.33 2305
```

2. Received prompt asking for hop count to `dash.ent1.pcte.mil`
3. Ran traceroute from red-internet-host:

```bash
traceroute dash.ent1.pcte.mil
```

4. Counted number of hops to final destination: 15 hops total

### Verified Answer

**Answer:** `15`

---

## Question 34 -- DMZ2 Discovery T3 Info 2

**Level:** 1 | **Points:** 10

### Prompt

> Flag is the answer to the question on TCP port 2800.
>
> Service Prompt: "How many Name Server Records (NS) does Defense Technical Information Center (dtic.mil) have?"

### Steps Taken

1. Interfaced with TCP port 2800:

```bash
echo "hello" | netcat 172.16.140.33 2800
```

2. Received prompt asking for NS record count for `dtic.mil`
3. Ran DNS query from red-internet-host:

```bash
dig NS dtic.mil +short
```

4. Identified 6 NS records:
   - `dns1.nipr.mil`
   - `dns2.nipr.mil`
   - `dns3.nipr.mil`
   - `dns4.nipr.mil`
   - `dns5.nipr.mil`
   - `dns6.nipr.mil`

### Verified Answer

**Answer:** `6`

---

## Question 35 -- Recon Objective: Geolocate DTIC (dtic.mil)

**Level:** 1 | **Points:** 10

### Prompt

> Flag is the answer to the question on TCP port 2828.

### Steps Taken

1. **DNS Resolution:**

```bash
dig dtic.mil
```

Returned 3 IPs:
- `214.48.252.101`
- `214.48.244.101`
- `214.48.248.101`

2. **ASN & WHOIS Lookup:**

```bash
whois 214.48.244.101
```

Registered to:
- **DoD Network Information Center**
- **City: Columbus**
- **State: OH**

3. **Cross-Verify with IP Geolocation:**

```bash
curl ipinfo.io/214.48.244.101
curl ip-api.com/json/214.48.244.101
```

Result: `"city": "Columbus", "region": "Ohio"`

4. **Confirm DTIC's Administrative Base:**
   - DTIC is a DoD entity, and its administrative registration is tied to Columbus, OH
   - This matches both WHOIS and IP geolocation data

### Verified Answer

**Answer:** `Columbus, OH`

---

## Question 36 -- DMZ2 Discovery T3 Info 4

**Level:** 1 | **Points:** 10

### Prompt

> Flag is the answer to the question on UDP port 2000.
>
> Service Prompt: "How many IPv6 prefixes are assigned to the Autonomous System that the Defense Technical Information Center (dtic.mil) is assigned to?"

### Steps Taken

1. Interfaced with UDP port 2000:

```bash
echo "hello" | nc -u 172.16.140.33 2000
```

2. Resolved `dtic.mil` to IP: `214.48.244.101`
3. Verified WHOIS output:
   - NetName: `DNIC-NET-214`
   - OrgName: `DoD Network Information Center`
4. Identified ASN: `AS27064`
5. Queried ASN prefix data via [bgp.he.net](https://bgp.he.net/AS27064)
   - IPv6 Prefixes Originated: **19**

### Verified Answer

**Answer:** `19`

---

## Question 37 -- DMZ2 Discovery T3 Info 5

**Level:** 1 | **Points:** 10

### Prompt

> Flag is the answer to the question on UDP port 2011.
>
> Service Prompt: "What is the DNS admin email for steampowered.com?"

### Steps Taken

1. Interfaced with UDP port 2011:

```bash
echo "hello" | nc -u 172.16.140.33 2011
```

2. Queried SOA record for `steampowered.com`:

```bash
dig steampowered.com SOA
```

3. Parsed SOA output:
   - Primary NS: `ns1.valvesoftware.com.`
   - Admin Email (DNS format): `admin-dns.valvesoftware.com.`
4. Interpreted DNS email format:
   - First dot becomes `@`, rest stay as dots
   - Converted: `admin-dns@valvesoftware.com`

**Reference:** https://viewdns.info/dnsrecord/?domain=steampowered.com

### Verified Answer

**Answer:** `admin-dns@valvesoftware.com`

---

## Question 38 -- DMZ2 Discovery T3 Info 6

**Level:** 1 | **Points:** 10

### Prompt

> Flag is the answer to the question on UDP port 2200.
>
> Service Prompt: "Who is the SSL Certificate issuer for LinkedIn (linkedin.com)?"

### Steps Taken

1. Interfaced with UDP port 2200:

```bash
echo "hello" | nc -u 172.16.140.33 2200
```

2. Queried SSL certificate via OpenSSL:

```bash
echo | openssl s_client -connect linkedin.com:443 -servername linkedin.com 2>/dev/null | openssl x509 -noout -issuer
```

3. Parsed issuer field:
   - `issuer=C = US, O = DigiCert Inc, CN = DigiCert SHA2 Secure Server CA`
4. Extracted Common Name (CN) as certificate authority

### Verified Answer

**Answer:** `DigiCert SHA2 Secure Server CA`

---

## Question 39 -- DMZ2 Discovery T3 Info 7

**Level:** 1 | **Points:** 10

### Prompt

> Flag is the answer to the question on UDP port 2250.
>
> Service Prompt: "Who is the manufacturer of the NIC with the MAC Address of (00:07:EC:E1:5D:18)?"

### Steps Taken

1. Interfaced with UDP port 2250:

```bash
echo "hello" | nc -u 172.16.140.33 2250
```

2. Extracted OUI prefix from MAC: `00:07:EC`
3. Queried OUI registry via public lookup:
   - Source: https://macaddress.io/macaddress/00:07:EC
4. Verified manufacturer match: Cisco Systems

### Verified Answer

**Answer:** `Cisco Systems`

---

## Question 40 -- DMZ2 Discovery T3 Info 8

**Level:** 1 | **Points:** 10

### Prompt

> Flag is the answer to the question on UDP port 2999.
>
> Service Prompt: "Who was the Vice-Chairman for Columbia County Board of Education (ccboe.net) at the start of 2006?"

### Steps Taken

1. Interfaced with UDP port 2999:

```bash
echo "hello" | nc -u 172.16.140.33 2999
```

2. Queried historical board roster via Wayback Machine:
   - Snapshot: https://web.archive.org/web/20051123144748/http://www.ccboe.net/BOE/home.html
3. Verified personnel listing:
   - Vice-Chairman: Mr. Wayne Bridges
   - District: 2
   - Contact: 855-5726

### Verified Answer

**Answer:** `Wayne Bridges`

---

## Question 41 -- DMZ2 Discovery T7 Ports

**Level:** 1 | **Points:** 5

### Prompt

> What well-known port(s) are open on the system?
>
> Format: Comma-separated, no spaces (e.g., `20,21,22,23,80`)

### Steps Taken

1. Ran Nmap scan on well-known ports (1-1023):

```bash
nmap -p 1-1023 172.16.140.35
```

2. Parsed output:
   - Host is up
   - 1022 ports closed
   - Open port: `22/tcp` (SSH)

### Verified Answer

**Answer:** `22`

---

## Question 42 -- DMZ2 Discovery T7 Hostname

**Level:** 1 | **Points:** 5

### Prompt

> What is the Hostname of the system?

### Steps Taken

1. Identified target system: `172.16.140.35`
2. Verified system is up via Nmap scan
3. SSH'd into system for direct enumeration:

```bash
ssh user@172.16.140.35
hostname
```

4. Confirmed hostname: `red-int-dmz2-host-2`

### Verified Answer

**Answer:** `red-int-dmz2-host-2`
