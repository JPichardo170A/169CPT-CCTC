# Movement and Redirection Challenges

---

## Task 1 - Netcat Relay

### Section Overview - Netcat Relays and Steganographic Extraction

- **Level:** 2
- **Points:** 40 (10 points per relay)
- **Prompt:** Utilize the targets T2 and RELAY to develop Netcat relays for use by Gorgan Cyber Forces. Named pipes must be used on RELAY to facilitate the transfer of steganographic image files to T1. Once each image is received on T1, extract the hidden message using the steghide tool. Then, generate a flag using `cat [phraseX.txt] | md5sum`. Each image has a known expected file size for verification.

### Hosts Involved

| Host | Role | IP Address |
|------|------|------------|
| T2 | Sender | 172.16.82.115 |
| RELAY | Middle | 172.16.40.10 |
| T1 | Receiver | 10.10.0.40 |

### Credentials

- **RELAY & T1:** student / password

### Steghide Syntax

```bash
steghide extract -sf [image name]
```
- **Passphrase:** `password`

### File Transfers Summary

| Relay | File | Port | Direction | Size (bytes) |
|-------|------|------|-----------|--------------|
| Relay 1 | 1steg.jpg | 1234 | T2 sends to RELAY | 89824 |
| Relay 2 | 2steg.jpg | 4321 | T2 sends to RELAY | 70723 |
| Relay 3 | 3steg.jpg | 6789 | T2 listens (RELAY connects) | - |
| Relay 4 | 4steg.jpg | 9876 | T2 listens (RELAY connects) | - |

---

## Question 1 - Netcat Relay T1

- **Level:** 2
- **Points:** 10
- **Prompt:** Transfer the image file 1steg.jpg from T2 to T1 using RELAY and named pipes. Extract the hidden message and generate flag1 using md5sum.

### Steps Taken

1. Identified host roles and IPs:
   - T2 = BLUE_HOST-4 (172.16.82.115)
   - RELAY = BLUE_INT_DMZ_HOST-1 (172.16.40.10)
   - T1 = INTERNET_HOST (10.10.0.40)

2. On T1 - set up listener:
   ```bash
   nc -lvp 9999 > 1steg.jpg
   ```

3. On RELAY - create named pipe and relay:
   ```bash
   mknod mypipe p
   nc -lvp 1234 < mypipe | nc 10.10.0.40 9999 > mypipe
   ```

4. Insider on T2 sent the file:
   ```bash
   nc 172.16.40.10 1234 < 1steg.jpg
   ```

5. Verified file on T1:
   ```bash
   ls -lh 1steg.jpg
   ```
   - Size: 89824 bytes

6. Extracted hidden message:
   ```bash
   steghide extract -sf 1steg.jpg
   ```
   - Passphrase: `password`

7. Generated MD5 hash:
   ```bash
   cat phrase1.txt | md5sum
   ```

**Answer/Flag:** `6abd7feac505f1384e6f98e3d1e8ba95`

---

## Question 2 - Netcat Relay T2

- **Level:** 2
- **Points:** 10
- **Prompt:** Transfer the image file 2steg.jpg from T2 to T1 using RELAY and named pipes. Extract the hidden message and generate flag2 using md5sum.

### Steps Taken

1. Identified host roles and IPs:
   - T2 = BLUE_HOST-4 (172.16.82.115)
   - RELAY = BLUE_INT_DMZ_HOST-1 (172.16.40.10)
   - T1 = INTERNET_HOST (10.10.0.40)

2. On T1 - set up listener:
   ```bash
   nc -lvp 9998 > 2steg.jpg
   ```

3. On RELAY - create named pipe and relay:
   ```bash
   mknod mypipe2 p
   nc -lvp 4321 < mypipe2 | nc 10.10.0.40 9998 > mypipe2
   ```

4. Insider on T2 sent the file:
   ```bash
   nc 172.16.40.10 4321 < 2steg.jpg
   ```

5. Verified file on T1:
   ```bash
   ls -lh 2steg.jpg
   ```
   - Size: 70723 bytes

6. Extracted hidden message:
   ```bash
   steghide extract -sf 2steg.jpg
   ```
   - Passphrase: `password`

7. Generated MD5 hash:
   ```bash
   cat phrase2.txt | md5sum
   ```

**Answer/Flag:** `024a1256e6f2ac6f08d2ccca2c3ba2a1`

---

## Question 3 - Netcat Relay T3

- **Level:** 2
- **Points:** 10
- **Prompt:** Transfer the image file 3steg.jpg from T2 to T1 using RELAY and named pipes. T2 is listening for a connection from RELAY on TCP port 6789. Extract the hidden message and generate flag3 using md5sum.

### Steps Taken

1. Identified host roles and IPs:
   - T2 = BLUE_HOST-4 (172.16.82.115)
   - RELAY = BLUE_INT_DMZ_HOST-1 (172.16.40.10)
   - T1 = INTERNET_HOST (10.10.0.40)

2. On T1 - set up listener:
   ```bash
   nc -lvp 9997 > 3steg.jpg
   ```

3. On RELAY - create named pipe and connect to T2 (T2 is listening):
   ```bash
   mknod mypipe3 p
   nc 172.16.82.115 6789 < mypipe3 | nc 10.10.0.40 9997 > mypipe3
   ```

4. Verified file on T1:
   ```bash
   ls -lh 3steg.jpg
   ```
   - Size: 177444 bytes

5. Extracted hidden message:
   ```bash
   steghide extract -sf 3steg.jpg
   ```
   - Passphrase: `password`

6. Generated MD5 hash:
   ```bash
   cat phrase3.txt | md5sum
   ```

**Answer/Flag:** `6fb3afb3a172f3605551c4ac05168a0c`

---

## Question 4 - Netcat Relay T4

- **Level:** 2
- **Points:** 10
- **Prompt:** Transfer the image file 4steg.jpg from T2 to T1 using RELAY and named pipes. T2 is listening for a connection from RELAY on TCP port 9876. Extract the hidden message and generate flag4 using md5sum.

### Steps Taken

1. Identified host roles and IPs:
   - T2 = BLUE_HOST-4 (172.16.82.115)
   - RELAY = BLUE_INT_DMZ_HOST-1 (172.16.40.10)
   - T1 = INTERNET_HOST (10.10.0.40)

2. On T1 - set up listener:
   ```bash
   nc -lvp 9996 > 4steg.jpg
   ```

3. On RELAY - create named pipe and connect to T2 (T2 is listening):
   ```bash
   mknod mypipe4 p
   nc 172.16.82.115 9876 < mypipe4 | nc 10.10.0.40 9996 > mypipe4
   ```

4. Verified file on T1:
   ```bash
   ls -lh 4steg.jpg
   ```
   - Size: 204283 bytes

5. Extracted hidden message:
   ```bash
   steghide extract -sf 4steg.jpg
   ```
   - Passphrase: `password`

6. Generated MD5 hash:
   ```bash
   cat phrase4.txt | md5sum
   ```

**Answer/Flag:** `378ee6771aa1cbd9653479bfc7db819e`

---

## Daily Tunnels Practice

**Section:** Tunnels - 1 - Avatar
**Assigned Float IP:** 10.50.176.142

---

## Task 2 - Tunnels Prep

- **Progress:** 0/20
- **Start Flag:** Provided by Mission Command

**Instructions:** Use the Tunnels Prep Diagram to answer the following twenty (20) questions. Supply only the letter for your answer.

---

### Tunnel Prep - Question 1

**Topic:** What is "localhost"?

**Question:** What is the word "localhost" associated with? (Max 2 Attempts)

**Answer:** C. Both A and B

**Explanation:**
- A. Loopback address - "localhost" refers to the loopback interface used for internal communication within a host.
- B. 127.0.0.1 - This is the IPv4 address assigned to the loopback interface.
- Therefore, C is correct because "localhost" is associated with both the loopback concept and the IP address 127.0.0.1.

---

### Tunnel Prep - Question 2

**Topic:** Alternate SSH Port

**Question:** If a user entered the following syntax on OPS$:

```bash
OPS$ ssh cctc@10.50.1.150 -p 1111
```

What is 1111 in relation to the command entered? (Max 2 Attempts)

**Answer:** B. alternate ssh port on 10.50.1.150

**Explanation:**
- The `-p` flag in the SSH command specifies the port number to connect to on the remote host.
- In this case, `1111` is the alternate SSH port on the remote system at `10.50.1.150`.
- This allows the user to connect to an SSH service running on a non-default port (default is 22).

---

### Tunnel Prep - Question 3

**Topic:** Alternate Port - Localhost Context

**Question:** If a user entered the following syntax on OPS$:

```bash
OPS$ ssh cctc@localhost -p 1111
```

What is 1111 in relation to the command entered? (Max 2 Attempts)

**Answer:** C. local listening port on OPS

**Explanation:**
- The command targets `localhost`, which refers to the local machine (OPS$).
- The `-p 1111` flag tells SSH to connect to port 1111 on the local host.
- Therefore, 1111 must be a locally listening SSH service or a tunnel endpoint.

---

### Tunnel Prep - Question 4

**Topic:** SSH Target IP - PC1 from OPS

**Question:** Using the Tunnels Prep Diagram, which IP would we use to SSH to PC1 from OPS?

```
ssh cctc@__________
```

**Answer:** `10.50.1.150`

**Explanation:**
- According to the Tunnels Prep Diagram, PC1 is reachable from OPS via the float IP address 10.50.1.150.
- This IP is used as the target in the SSH command to initiate a connection from OPS to PC1.

---

### Tunnel Prep - Question 5

**Topic:** Dynamic SSH Tunnel

**Question:** Which ssh syntax would properly setup a Dynamic tunnel to PC1? (Max 2 Attempts)

**Answer:** C. `ssh cctc@10.50.1.150 -D 9050 -NT`

**Explanation:**
- The `-D` flag sets up a dynamic SOCKS proxy tunnel, allowing flexible routing through the remote host.
- `9050` is the local port where the SOCKS proxy will listen.
- `10.50.1.150` is the IP address of PC1, as shown in the Tunnels Prep Diagram.
- `-NT` disables remote command execution and terminal allocation, ideal for tunneling.

---

### Tunnel Prep - Question 6

**Topic:** Local SSH Tunnel to PC1

**Question:** Which ssh syntax would properly setup a Local tunnel to PC1 SSH port? (Max 2 Attempts)

**Answer:** A. `ssh -L 1111:localhost:22 cctc@10.50.1.150 -NT`

**Explanation:**
- The `-L` flag sets up a local port forwarding tunnel.
- `1111:localhost:22` means port 1111 on the local machine will forward to port 22 (SSH) on localhost from the remote host's perspective.
- `cctc@10.50.1.150` connects to PC1, where the tunnel endpoint is established.
- `-NT` disables remote command execution and terminal allocation, ideal for tunneling.

---

### Tunnel Prep - Question 7

**Topic:** Local Tunnel to HTTP Port

**Question:** Which ssh syntax would properly setup a Local tunnel to PC1 HTTP port? (Max 2 Attempts)

**Answer:** B. `ssh cctc@10.50.1.150 -L 1111:localhost:80 -NT`

**Explanation:**
- The `-L` flag sets up a local port forwarding tunnel.
- `1111:localhost:80` means port 1111 on the local machine will forward to port 80 (HTTP) on localhost from the remote host's perspective.
- `cctc@10.50.1.150` connects to PC1, where the tunnel endpoint is established.
- `-NT` disables remote command execution and terminal allocation, ideal for tunneling.

---

### Tunnel Prep - Question 8

**Topic:** Dynamic Tunnel via Local Port Forward

**Question:** Which ssh syntax would allow us to establish a Dynamic tunnel using the Local tunnel created in Question 6? (Max 2 Attempts)

**Answer:** D. `ssh -D 9050 cctc@localhost -p 1111 -NT`

**Explanation:**
- In Question 6, we created a local tunnel that forwards port 1111 to PC1's SSH port.
- To use that tunnel as a gateway for a dynamic SOCKS proxy, we connect to localhost on port 1111.
- `-D 9050` sets up a SOCKS proxy on local port 9050.
- `-p 1111` tells SSH to use the forwarded port.
- `-NT` disables remote shell and terminal allocation, ideal for tunneling.

---

### Tunnel Prep - Question 9

**Topic:** Pulling HTTP Through Local Tunnel

**Question:** Which syntax would allow us to download the webpage of PC1 using the Local tunnel created in Question 7? (Max 2 Attempts)

**Answer:** C. `wget -r http://localhost:1111`

**Explanation:**
- In Question 7, a local tunnel was created to forward port 1111 to PC1's HTTP port (port 80).
- To access PC1's webpage through that tunnel, we target localhost:1111, which maps to PC1's HTTP service.
- `wget -r` recursively downloads the webpage content.

---

### Tunnel Prep - Question 10

**Topic:** Pulling HTTP Through Dynamic Tunnel

**Question:** Which syntax would allow us to download the webpage of PC2 using the Dynamic tunnel created in Question 8? (Max 2 Attempts)

**Answer:** B. `proxychains wget -r http://100.1.1.2`

**Explanation:**
- In Question 8, a dynamic SOCKS proxy was created using SSH and port 9050.
- `proxychains` is used to route traffic through that SOCKS proxy.
- `wget -r http://100.1.1.2` targets PC2's web service directly, and proxychains ensures the request is tunneled through the dynamic proxy.

---

### Tunnel Prep - Question 11

**Topic:** Local Tunnel to Second Pivot (PC2 via PC1)

**Question:** Which ssh syntax would properly setup a Local tunnel to PC2 SSH port using PC1 as your pivot? (Max 2 Attempts)

**Answer:** D. `ssh cctc@10.50.1.150 -L 1111:100.1.1.2:22 -NT`

**Explanation:**
- This command sets up a local tunnel from OPS$ to PC2's SSH port through PC1.
- `cctc@10.50.1.150` connects to PC1, which acts as the pivot.
- `-L 1111:100.1.1.2:22` forwards local port 1111 to PC2's SSH port via PC1.
- `-NT` disables remote shell and terminal allocation, ideal for tunneling.

---

### Tunnel Prep - Question 12

**Topic:** Second Local Tunnel via First Local SSH

**Question:** Which ssh syntax would properly setup a 2nd Local tunnel to PC2 SSH port using the tunnel made in Question 6 as your first tunnel? (Max 2 Attempts)

**Answer:** A. `ssh -L 2222:100.1.1.2:22 cctc@localhost -p 1111 -NT`

**Explanation:**
- In Question 6, a local tunnel was created to PC1's SSH port via port 1111.
- This command connects to localhost on port 1111, which routes through PC1.
- The `-L 2222:100.1.1.2:22` sets up a second local tunnel from OPS$ to PC2's SSH port.
- `-NT` disables remote shell and terminal allocation, ideal for tunneling.

---

### Tunnel Prep - Question 13

**Topic:** Second Local Tunnel to PC2 HTTP via First Local SSH

**Question:** Which ssh syntax would properly setup a 2nd Local tunnel to PC2 HTTP port using the tunnel made in Question 6 as your first tunnel? (Max 2 Attempts)

**Answer:** B. `ssh cctc@localhost -p 1111 -L 2222:100.1.1.2:80 -NT`

**Explanation:**
- In Question 6, a local tunnel was created to PC1's SSH port via port 1111.
- This command connects to localhost on port 1111, which routes through PC1.
- The `-L 2222:100.1.1.2:80` sets up a second local tunnel from OPS$ to PC2's HTTP port.
- `-NT` disables remote shell and terminal allocation, ideal for tunneling.

---

### Tunnel Prep - Question 14

**Topic:** Dynamic Tunnel via Second Local Port

**Question:** Which ssh syntax would allow us to establish a Dynamic tunnel using the Local tunnel created in Question 12? (Max 2 Attempts)

**Answer:** A. `ssh -D 9050 cctc@localhost -p 2222 -NT`

**Explanation:**
- In Question 12, a local tunnel was created to forward port 2222 to PC2's SSH port.
- This command connects to localhost on port 2222, which routes through the second tunnel to PC2.
- `-D 9050` sets up a SOCKS proxy on local port 9050.
- `-NT` disables remote shell and terminal allocation, ideal for tunneling.

---

### Tunnel Prep - Question 15

**Topic:** Troubleshooting Dynamic Tunnel Failure

**Question:** An Admin created the following tunnels but found that the Dynamic tunnel would not connect. Where did the Admin make the error? (Max 2 Attempts)

```bash
1.) ssh cctc@10.50.1.150 -L 1234:100.1.1.2:22 -NT
2.) ssh -D 9050 cctc@100.1.1.2 -p 1234 -NT
```

**Answer:** C. authenticated to wrong IP in line 2

**Explanation:**
- Line 1 correctly sets up a local tunnel from OPS$ to PC2's SSH port via PC1.
- Port 1234 on OPS$ is now mapped to PC2's SSH service.
- In Line 2, the admin tries to connect directly to 100.1.1.2, which is PC2's IP.
- However, the tunnel endpoint is on localhost:1234, not on PC2 directly.
- The correct command should authenticate to localhost, not 100.1.1.2.

---

### Tunnel Prep - Question 16

**Topic:** Troubleshooting Telnet Tunnel to PC3

**Question:** From the OPS workstation, the Admin is trying to telnet through the tunnels to PC3. The Admin created the following tunnels but found that the telnet connection would not connect. Where did the Admin make the error? (Max 2 Attempts)

```bash
1.) ssh cctc@10.50.1.150 -L 1234:192.168.2.1:22 -NT
2.) ssh -L 4321:192.168.2.2:23 cctc@localhost -p 1234 -NT
3.) telnet localhost 4321
```

**Answer:** ?

---

### Tunnel Prep - Question 17

**Topic:** Third Local Tunnel to PC3 TELNET via Multi-Hop

**Question:** Which ssh syntax would properly setup a 3rd Local tunnel to PC3 TELNET port using the tunnels made in Question 6 and Question 12? (Max 2 Attempts)

**Answer:** D. `ssh -p 2222 cctc@localhost -L 3333:192.168.2.2:23 -NT`

**Explanation:**
- In Question 6, a local tunnel was created to PC1's SSH port via port 1111.
- In Question 12, a second tunnel was created to PC2's SSH port via port 2222.
- This command connects to localhost on port 2222, which routes through PC2.
- The `-L 3333:192.168.2.2:23` sets up a third local tunnel to PC3's TELNET port (23).
- `-NT` disables remote shell and terminal allocation, ideal for tunneling.

---

### Tunnel Prep - Question 18

**Topic:** Telnet to PC3 via Third Local Tunnel

**Question:** Which syntax would allow us to telnet to PC3 using the tunnel made in Question 17? (Max 2 Attempts)

**Answer:** B. `telnet localhost 3333`

**Explanation:**
- In Question 17, a local tunnel was created to forward port 3333 to PC3's Telnet port (23).
- To access PC3 via Telnet, you connect to localhost on port 3333, which routes through the tunnel.
- The correct syntax for Telnet is: `telnet [host] [port]`, not using `:` or `-p`.

---

### Tunnel Prep - Question 19

**Topic:** Remote Tunnel from PC3 to PC2

**Question:** Which syntax would properly setup a Remote tunnel from PC3 back to PC2 using PC3 SSH port as the target? (Max 2 Attempts)

**Answer:** ?

**Explanation:**
- This command is run from OPS$, connecting to PC3's SSH port via the tunnel on port 3333.
- `-R 4444:localhost:22` sets up a remote tunnel, allowing PC2 to connect to PC3's SSH port by accessing port 4444 on PC2.
- `cctc@localhost -p 3333` connects to PC3 through the layered tunnels created earlier.
- `-NT` disables remote shell and terminal allocation, ideal for tunneling.
- This setup allows PC2 to initiate SSH connections back to PC3 via port 4444.

---

### Tunnel Prep - Question 20

**Topic:** Local Tunnel to Remote Tunnel Endpoint

**Question:** Which syntax would properly setup a Local tunnel to map to the tunnel made in Question 19 using the tunnel made in Question 6 and Question 12? (Max 2 Attempts)

**Answer:** A. `ssh cctc@localhost -p 2222 -L 5555:localhost:4444 -NT`

**Explanation:**
- In Question 19, a remote tunnel was created from PC3 back to PC2, exposing PC3's SSH port on PC2's port 4444.
- In Question 6 and 12, tunnels were built to reach PC2 via localhost:2222.
- This command connects to localhost on port 2222, which routes through PC2.
- The `-L 5555:localhost:4444` maps a local port 5555 on OPS$ to PC2's port 4444, which is linked to PC3's SSH.
- `-NT` disables remote shell and terminal allocation, ideal for tunneling.

---

## Task 3 - Tunnels Training

**Task:** Donovian Tunnels Training: dig_dug_dig_dug

- **Your Credentials:** net7_student10@10.50.11.222 : password10
- **X** = Your student number
- **T3 (Atropia) Float IP Address:** 10.50.11.222
- **T4 (Pineland) Float IP Address:** 10.50.200.173
  - Note: You can only **Telnet** to T4 to act as an insider. This will **not** be a routed path.
- **Progress:** 0/20

---

### Question 1 - Tunnels Training: Cortina HTTP

- **Level:** I
- **Context:** T3 is the authorized initial pivot. The flag is the answer to the question found on Cortina's HTTP Server.

#### Step 1 - Scan for Open Ports via SOCKS Proxy

```bash
proxychains ./scan.sh
```

- **Target Subnet:** 10.3.0.0/24
- **Ports Scanned:** 21-23 80

**Results:**

| Host | Open Ports |
|------|------------|
| 10.3.0.1 | FTP (21), HTTP (80) |
| 10.3.0.27 | FTP (21), HTTP (80) |
| 10.3.0.10 | SSH (22) |

#### Step 2 - Retrieve Cortina's HTTP Page

```bash
proxychains wget -r http://10.3.0.1
```

- File saved: `10.3.0.1/index.html`

#### Step 3 - Read Cortina's HTTP Content

```bash
cat 10.3.0.1/index.html
```

**Content:** "You have accessed Cortina's official website. The flag is the answer to the following question: Which steganography mode is used when a message is added by modifying the least significant bits in a file?"

**Answer/Flag:** `substitution`

---

### Question 2 - Tunnels Training: Cortina FTP

- **Level:** I
- **Context:** T3 is the authorized initial pivot. The flag is the answer to the question found on Cortina's FTP Server.

#### Step 1 - Retrieve Cortina's FTP Directory Listing

```bash
proxychains wget -r ftp://10.3.0.1 21
```

- Connected to 10.3.0.1:21 via SOCKS proxy
- Logged in as anonymous
- Retrieved files: `flag.txt`, `welcome.msg`

#### Step 2 - Navigate to Downloaded Directory

```bash
cd 10.3.0.1
ls
```

Files Present: `flag.txt`, `index.html`, `welcome.msg`

#### Step 3 - Read Cortina's FTP Content

```bash
cat flag.txt
```

**Content:** "You have accessed Cortina's FTP server. The flag is the answer to the following question: A malicious actor adds a hidden message into a file's unused spaces. Which Steganography mode is being used?"

**Answer/Flag:** `injection`

---

### Question 3 - Tunnels Training: Victoria HTTP

- **Level:** I
- **Context:** T3 is the authorized initial pivot. The flag is the answer to the question found on Victoria's HTTP Server.

#### Step 1 - Scan for Open Ports via SOCKS Proxy

```bash
proxychains ./scan.sh
```

- **Target Subnet:** 10.3.0.0/24
- **Ports Scanned:** 21-23 80

**Results:**

| Host | Open Ports |
|------|------------|
| 10.3.0.27 | FTP (21), HTTP (80) |
| 10.3.0.10 | SSH (22) |
| 10.3.0.1 | FTP (21), HTTP (80) |
| 10.3.0.254 | HTTP (80) |

#### Step 2 - Retrieve Victoria's HTTP Page

```bash
proxychains wget -r http://10.3.0.27
```

- File saved: `10.3.0.27/index.html`

#### Step 3 - Read Victoria's HTTP Content

```bash
cat index.html
```

**Content:** "You have accessed Victoria's HTTP server. The flag is the answer to the following question: Which IPv6 transition tunneling protocol establishes the endpoints automatically and uses a special prefix of 2002:[IPv4_address]::/48?"

**Answer/Flag:** `6to4`

---

### Question 4 - Tunnels Training: Victoria FTP

- **Level:** I
- **Context:** T3 is the authorized initial pivot. The flag is the answer to the question found on Victoria's FTP Server.

#### Steps Taken

```bash
proxychains wget -r ftp://10.3.0.27 21
cd 10.3.0.27
cat flag.txt
```

**Flag Prompt:** "Which IPv6 transition tunneling protocol uses a special IPv6 prefix (2001::/32) to provide IPv6 connectivity through IPv4 networks, specifically for devices behind NAT?"

**Answer/Flag:** `teredo`

---

### Question 5 - Tunnels Training: Pineland

- **Level:** I
- **Host:** Pineland (Compromised within Donovia)
- **Access:** Telnet to T4
- **Objective:** Conduct passive recon and locate shared CCTC data

#### Steps Taken

```bash
find / -name hint* 2> /dev/null
find / -name flag* 2> /dev/null
```

**Flag Prompt:** "Conduct passive recon on this host and find the shared location for data relating to CCTC."

**Answer/Flag:** `~/.ssh/known_hosts`

---

### Question 6 - Tunnels Training: Mohammed HTTP

- **Objective:** Access Mohammed's HTTP server hosted on T3 and answer the question displayed on the webpage.

#### Network Overview

| Host | Hostname | Inside IP | Floating IP |
|------|----------|-----------|-------------|
| Internet_Host | - | 10.10.0.40 | - |
| T4 (Pineland) | tunnels-training-pineland-insider | 10.2.0.1 | 10.50.200.173 |
| T3 (Atropia) | tunnels-training-atropia-pivot | 10.3.0.10 | 10.50.11.222 |

#### Tunnel Setup

1. Telnet into T4 from Internet_Host:
   ```bash
   telnet 10.50.200.173
   ```

2. SSH from T4 to T3 with Remote Port Forward:
   ```bash
   ssh net7_student10@10.3.0.10 -R 21000:10.2.0.1:22
   ```

3. Local Port Forward from Internet_Host to T3:
   ```bash
   ssh net7_student10@10.50.11.222 -L 21001:localhost:21000
   ```

4. SOCKS Proxy from Internet_Host to T4:
   ```bash
   ssh net7_student10@localhost -p 21001 -D 9050
   ```

#### Recon and Enumeration

5. Scan the T4 subnet via proxychains:
   ```bash
   proxychains ./scan.sh
   ```
   - Target subnet: 10.2.0.0/24
   - Found HTTP service on: 10.2.0.2

6. Access Mohammed's HTTP Server:
   ```bash
   proxychains wget -r http://10.2.0.2
   cd 10.2.0.2
   cat index.html
   ```

**Challenge Question:** "What file is used to modify the SSH server parameters for Linux? (provide the complete path)"

**Answer/Flag:** `/etc/ssh/sshd_config`

---

### Question 7 - Tunnels Training: Mohammed FTP

- **Objective:** Access Mohammed's FTP server hosted on T3 and answer the question displayed in the FTP directory.

#### Network Overview

| Host | Hostname | Inside IP | Floating IP |
|------|----------|-----------|-------------|
| Internet_Host | - | 10.10.0.40 | - |
| T4 (Pineland) | tunnels-training-pineland-insider | 10.2.0.1 | 10.50.200.173 |
| T3 (Atropia) | tunnels-training-atropia-pivot | 10.3.0.102 | 10.50.11.222 |

#### Tunnel Setup

1. Telnet into T4 from Internet_Host:
   ```bash
   telnet 10.50.200.173
   ```

2. SSH from T4 to T3 with Remote Port Forward:
   ```bash
   ssh net7_student10@10.3.0.10 -R 21000:10.2.0.1:22
   ```

3. Local Port Forward from Internet_Host to T3:
   ```bash
   ssh net7_student10@10.50.11.222 -L 21001:localhost:21000
   ```

4. SOCKS Proxy from Internet_Host to T4:
   ```bash
   ssh net7_student10@localhost -p 21001 -D 9050
   ```

#### Recon and Enumeration

5. Scan the T4 subnet via proxychains:
   ```bash
   proxychains ./scan.sh
   ```
   - Target subnet: 10.2.0.0/24
   - Found FTP service on: 10.2.0.2

6. Access Mohammed's FTP Server:
   ```bash
   proxychains ftp 10.2.0.2
   ```
   - Login anonymously or with provided credentials
   - Navigate and retrieve the question file

**Challenge Question:** "What file is used to modify the SSH client parameters for Linux? (provide the complete path)"

**Answer/Flag:** `/etc/ssh/ssh_config`

---

### Question 8 - Tunnels Training: Mojave FTP

- **Level:** II

#### Authorized Pivot

- **T3 (Atropia)**
- **Floating IP:** 10.50.11.222
- **Internal IP:** 10.3.0.10

#### Credentials

- **Username:** net7_student10
- **Password:** 10
- **Authorized Port Range:** 71000-71099

#### Steps Taken

1. SSH into T3 from Internet_Host:
   ```bash
   ssh net7_student10@10.50.11.222
   ```

2. Verified internal IP and hostname:
   ```bash
   hostname
   ```
   - tunnels-training-atropia-pivot
   ```bash
   ip a
   ```
   - eth0: 10.3.0.10/24

3. Checked subnet reachability:
   ```bash
   ping 10.4.0.1   # Reachable
   ping 10.5.0.1   # Unreachable
   ```

4. Created local port forward to Mojave candidate:
   ```bash
   ssh net7_student10@10.50.11.222 -L 21000:10.4.0.1:22
   ```

5. Created SOCKS proxy to T3:
   ```bash
   ssh net7_student10@localhost -p 21000 -D 9050
   ```

6. Used proxychains to scan Mojave subnet:
   ```bash
   proxychains ./scan.sh
   ```
   - Found FTP and HTTP open on 10.5.0.1 and 10.5.0.57

7. Accessed Mojave web server:
   ```bash
   proxychains wget -r http://10.5.0.1
   ```
   - Retrieved index.html

8. Accessed Mojave FTP server:
   ```bash
   proxychains wget -r ftp://10.5.0.1
   ```
   - Retrieved flag.txt and welcome.msg

**Challenge Question:** "Multiplexing of multiple logical channels and X11 forwarding over a single SSH connection is a responsibility of which component of SSH as defined in RFC 4251?"

**Answer/Flag:** `SSH Connection Protocol`

---

### Question 9 - Tunnels Training: Mojave HTTP

- **Level:** II

#### Authorized Pivot

- **T3 (Atropia)**
- **Floating IP:** 10.50.11.222
- **Internal IP:** 10.3.0.10

#### Credentials

- **Username:** net7_student10
- **Password:** 10
- **Authorized Port Range:** 71000-71099

#### Steps Taken

1. SSH into T3 from Internet_Host:
   ```bash
   ssh net7_student10@10.50.11.222
   ```

2. Verified subnet reachability:
   ```bash
   ping 10.4.0.1   # Reachable
   ping 10.5.0.1   # Unreachable via ICMP, but reachable via proxychains
   ```

3. Created SOCKS proxy to T3:
   ```bash
   ssh net7_student10@10.50.11.222 -D 9050
   ```

4. Used proxychains to scan Mojave subnet:
   ```bash
   proxychains ./scan.sh
   ```
   - Found HTTP open on 10.5.0.1

5. Accessed Mojave HTTP server:
   ```bash
   proxychains wget -r http://10.5.0.1
   ```
   - Retrieved index.html

6. Read the question from Mojave's web server:
   ```bash
   cat 10.5.0.1/index.html
   ```

**Challenge Question:** "Name of the SSH component defined in RFC 4251 that is responsible for the Key Exchange (KEX) process?"

**Answer/Flag:** `SSH Transport Layer Protocol`

---

### Question 10 - Tunnels Training: Parumphia FTP

- **Level:** III

#### Authorized Pivot

- **T3 (Atropia)**
- **Floating IP:** 10.50.11.222
- **Internal IP:** 10.3.0.10

**Clue:** Parumphia is co-located with Mojave

**Target IP:** Parumphia FTP Server: 10.5.0.57

#### Steps Taken

1. Created SOCKS proxy to T3:
   ```bash
   ssh net7_student10@10.50.11.222 -D 9050
   ```

2. Used proxychains to access Parumphia FTP:
   ```bash
   proxychains wget -r ftp://10.5.0.57
   ```
   - Retrieved flag.txt and welcome.msg

3. Read the question from flag.txt:
   ```bash
   cat 10.5.0.57/flag.txt
   ```
   - "Using the following command `ssh student@10.10.0.40 -R 4444:192.168.1.10:22`. On which system is port 4444 created?"

4. Analyzed the SSH Remote Port Forward syntax:
   - `-R 4444:192.168.1.10:22` means:
     - Port 4444 is created on the **remote system** (i.e., 10.10.0.40)
     - It forwards to 192.168.1.10:22 from there

**Answer/Flag:** `10.10.0.40`
