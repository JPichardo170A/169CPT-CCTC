# Data Collection Challenges

---

## Task 4 - Data Collection: T5 Access

- **Level:** I
- **Target System:** T5
- **Floating IP:** 10.50.49.85

### Credentials

- **Username:** net7_student10
- **Password:** 10

### Objective

Find a way to connect to T5 and identify the port number that allowed initial access.

### Steps Taken

1. Attempted SSH access:
   ```bash
   ssh net7_student10@10.50.49.85
   ```
   - No response or blocked

2. Ran port scan:
   ```bash
   ./scan.sh
   ```
   - Target: 10.50.49.85
   - Ports scanned: 21-23 80
   - Result: Port 23 (telnet) open

3. Connected via Telnet:
   ```bash
   telnet 10.50.49.85
   ```
   - Connected successfully
   - Logged in with credentials:
     - Username: net7_student10
     - Password: 10

4. Verified system access:
   - Hostname: data-collection-net-ssh-01
   - OS: Debian GNU/Linux 10

**Answer/Flag:** `23`

---

## Question 1 - SSH-01 Flag

- **Level:** 1
- **Points:** 5
- **Prompt:** "Host SSH-01 has a flag hosted on an open service port not seen from the outside. The flag is the answer to the question you find on SSH-01."

### Steps Taken

1. Scanned T5 (`10.50.49.85`) and found TCP port `23` (Telnet) open

2. Connected to SSH-01 using:
   ```bash
   telnet 10.50.49.85
   ```

3. Logged in with credentials:
   - Username: `net7_student10`
   - Password: `10`

4. Identified internal subnet using:
   ```bash
   ip a
   ```
   - Internal IP: `192.168.0.10/24`

5. Created remote port forward to Internet_Host:
   ```bash
   ssh student@10.50.0.182 -R 21000:localhost:22
   ```

6. From Internet_Host, created SOCKS proxy:
   ```bash
   ssh net7_student10@localhost -p 21000 -D 9050
   ```

7. Scanned internal subnet `192.168.0.0/24` and found TCP port `80` open on `192.168.0.1`

8. Accessed internal HTTP service via proxychains:
   ```bash
   proxychains wget -r http://localhost
   ```

9. Retrieved and opened image file: `localhost/flag.png`

10. Read question: "Who developed the original SSH protocol?"

11. Answered with: `Tatu Ylonen`

**Answer/Flag:** `Tatu Ylonen`

### Notes

- SSH-01 was only accessible via Telnet from the outside
- Internal subnet was discovered using `ip a`
- SOCKS proxy enabled access to internal-only services
- Flag was embedded in an image served by the internal HTTP service

---

## Question 2 - SSH-03 Flag

- **Level:** 1
- **Points:** 5
- **Prompt:** "Host SSH-03 has a flag hosted on an open service port. The flag is the answer to the question you find on SSH-03."

### Steps Taken

1. Ran internal scan using:
   ```bash
   proxychains ./scan.sh
   ```
   - Found TCP port `21` (FTP) open on `192.168.0.20`

2. Accessed FTP service anonymously using:
   ```bash
   proxychains wget -r ftp://192.168.0.20
   ```

3. Retrieved files:
   - `flag.png`
   - `welcome.msg`

4. Viewed flag image using:
   ```bash
   eog flag.png
   ```

5. Read question: "Where did the inventor of SSH work?"

6. Answered with: `Finland`

**Answer/Flag:** `Finland`

### Notes

- FTP service was exposed internally on SSH-03
- Anonymous login allowed full directory listing and file retrieval
- Flag was embedded in an image file served via FTP

---

## Question 3 - Net-SSH-02 IP Address (SSH-02 Pivot)

- **Level:** 2
- **Points:** 5
- **Prompt:** "What is the IP Address of Net-SSH-02? It has HTTP & FTP open and has access to a single machine hidden behind it."

### Steps Taken

1. Ran internal scan using:
   ```bash
   proxychains ./scan.sh
   ```
   - Found multiple hosts with open ports
   - `192.168.0.20` had both TCP ports `21` (FTP) and `80` (HTTP) open

2. Accessed HTTP service on `192.168.0.20` using:
   ```bash
   proxychains wget -r http://192.168.0.20
   ```
   - Retrieved: `index.html`, `hint.png`

3. Viewed hint image using:
   ```bash
   eog hint.png
   ```
   - Message: "SSH is using an alternate port above the well-known ports. Use banner grabbing to figure it out so you can pivot to the next machine that only this one has access to."

4. Performed banner grabbing on FTP and HTTP ports:
   ```bash
   proxychains nc 192.168.0.20 21
   ```
   - Banner: `220 ProFTPD Server (Debian)`

   ```bash
   proxychains nc 192.168.0.20 80
   ```
   - Response: `<html><a href="./hint.png">hint.png</a></html>`

**Answer/Flag:** `192.168.0.20`

### Notes

- Net-SSH-02 exposes both FTP and HTTP services internally
- Hint confirmed SSH is running on a non-standard port
- Banner grabbing validated service identity and pivot potential

---

## Question 4 - SSH-02 Flag

- **Level:** 1
- **Points:** 5
- **Prompt:** "Host SSH-02 has a flag hosted on an open service port. The flag is the answer to the question you find on SSH-02."

### Steps Taken

1. Accessed HTTP service on `192.168.0.20`

2. Retrieved and viewed hint file:
   - `hint.png`
   - Message: "SSH is using an alternate port above the well known ports. Use banner grabbing to figure it out so you pivot to the next machine that only this one has access to."

3. Performed banner grabbing across high ports using:
   ```bash
   proxychains nc 192.168.0.20 [port]
   ```

4. Identified alternate SSH port and connected

5. Pivoted to hidden machine and retrieved flag

6. Read question: "Where did the inventor of SSH work?"

7. Answered with: `Helsinki University of Technology`

**Answer/Flag:** `Helsinki University of Technology`

### Notes

- SSH-02 uses a non-standard SSH port above 1024
- Banner grabbing was required to identify the correct port
- Pivoting enabled access to a hidden machine containing the flag

---

## Question 5 - SSH-05 Flag

- **Level:** 2
- **Points:** 5
- **Prompt:** "Host SSH-05 has a flag hosted on an open service port. This system is hidden behind Net-SSH-02. The flag is the answer to the question you find on SSH-05."

### Steps Taken

1. Scanned internal subnet using:
   ```bash
   proxychains ./scan.sh
   ```
   - Found TCP ports `21` (FTP) and `80` (HTTP) open on `192.168.0.50`

2. Accessed FTP service anonymously using:
   ```bash
   proxychains wget -r ftp://192.168.0.50
   ```
   - Retrieved: `flag.png`, `welcome.msg`

3. Viewed flag image using:
   ```bash
   eog flag.png
   ```

4. Read question: "Which RFC formalized the SSH-1 protocol architecture?"

5. Answered with: `RFC 4251`

**Answer/Flag:** `RFC 4251`

### Notes

- SSH-05 was discovered behind Net-SSH-02
- FTP service allowed anonymous access to retrieve the flag
- Flag was embedded in an image file containing the RFC question

---

## Question 6 - SSH-04 Flag

- **Level:** 1
- **Points:** 5
- **Prompt:** "Host SSH-04 has a flag hosted on an open service port. The flag is the answer to the question you find on SSH-04."

### Steps Taken

1. Scanned internal subnet and found TCP port `80` open on `192.168.0.40`

2. Accessed HTTP service and retrieved:
   - `flag.png`

3. Viewed flag image using:
   ```bash
   eog flag.png
   ```

4. Read question: "Which symmetric encryption algorithm is commonly used in SSH?"

5. Answered with: `AES`

**Answer/Flag:** `AES`

### Notes

- SSH-04 was identified by its open HTTP service
- Flag was embedded in an image file served by the host
- AES (Advanced Encryption Standard) is widely used for secure symmetric encryption in SSH

---

## Question 7 - SSH-04 Pivot

- **Level:** I
- **Points:** 10
- **Prompt:** "SSH-04 is another potential pivot. What other subnet does Net-SSH-04 have access to?"

### Steps Taken

1. From the Internet Host (`blue-internet-host`), connected to the Telnet Pivot Node:
   ```bash
   telnet 10.50.49.85
   ```
   - Login Username: net7_student10

2. From Telnet Pivot (`SSH-01`), scanned the internal subnet `192.168.0.0/24`:
   ```bash
   proxychains ./scan.sh
   ```
   - Network: 192.168.0
   - Range: 10-60
   - Ports: 10-6000

3. Discovered host `192.168.0.40` with open ports:
   - 80 (HTTP)
   - 5555 (SSH)

4. Accessed the HTTP service on `192.168.0.40`:
   ```bash
   proxychains wget -r http://192.168.0.40
   ```
   - Retrieved: `index.html`, `hint.png`, `flag.png`

5. Viewed `hint.png`:
   - Message: "This machine has access to the 172.16.0.0/24 network. You will have to find the alternate sshd port like on the net-ssh-02 to progress further."

6. Verified SSH service on port 5555:
   ```bash
   proxychains nc 192.168.0.40 5555
   ```
   - Banner: `SSH-2.0-OpenSSH_7.9p1 Debian-10+deb10u2`

7. Connected to `SSH-04` from `SSH-01`:
   ```bash
   ssh net7_student10@192.168.0.40 -p 5555
   ```

8. From `SSH-04`, confirmed access to the `172.16.0.0/24` subnet:
   ```bash
   ip a
   ip route
   ```

**Answer/Flag:** `172.16.0.0/24`

### Notes

- SSH-04 is only reachable from SSH-01
- Port 5555 was used for SSH access
- Subnet access confirmed via hint file and routing table
- All enumeration and scanning was looped back to blue-internet-host using tunnels
