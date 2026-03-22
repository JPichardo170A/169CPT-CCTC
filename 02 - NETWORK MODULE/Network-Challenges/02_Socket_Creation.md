# Networking - 2 - Socket Creation and Packet Manipulation

Challenges covering Address Families, Connections, Raw Sockets, and Python3 socket scripting.

---

## Question 1 - Address Families

**Prompt:**
What are the 3 Address Families associated with the python3 socket module?

**Answer:** `socket.AF_INET,socket.AF_INET6,socket.AF_UNIX`

---

## Question 2 - Connections

**Prompt:**
What are the two socket functions called to open a connection and to disconnect from that connection?

**Answer:** `socket.connect(),socket.close()`

---

## Question 3 - Raw Socket Packet Assembly

**Prompt:**
What python3 library function is utilized to combine the various pieces of your raw socket packet into network order?

**Answer:** `struct.pack()`

---

## Question 4 - Missing Data

**Prompt:**
What must be manually created with raw sockets that stream and datagram sockets creates for you?

**Answer:** `header`

---

## Question 5 - Send Without Connection

**Prompt:**
What function within the socket module allows you to send data to a socket, while not already being connected to a remote socket?

**Answer:** `socket.sendto()`

---

## Question 6 - Datagram and Stream Socket Requirements

**Prompt:**
Provide an example of the two required items needed to be set in order to send a Datagram or Stream socket? (excluding any of the socket.socket functions)

**Answer:** `address,port`

---

## Question 8 - Objects

**Prompt:**
When sending data across a connection, what must a string be converted to before being sent due to encoding?

**Answer:** `bytes`

---

## Question 9 - Stream Socket Message Sender

**Prompt:**
Gorgan Forces have requested you get a message to one of their remote teams that are utilizing the BLUE_DMZ_HOST-1. Utilizing the criteria they provided, generate a stream socket with python3:

Coded information was placed into the video below. Look at the note the woman passes to the man.
https://youtu.be/6WTdTwcmxyo?t=35

From your INTERNET-HOST to the BLUE_DMZ_HOST-1:
- IP address = 172.16.1.15
- Port number = 5309
- Message = Message (First letter capitalized)

**Steps:**
- Open nano stream.py
- Adjust IP and port
- Change message
- Save, exit, and run the file in Python 3
- Boom -- your answer

**Python3 Script Used:**

```python
#!/usr/bin/python3
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

ipaddr = '172.16.1.15'
port = 5309

s.connect((ipaddr, port))
s.send(b'Message\n')

data, conn = s.recvfrom(1024)
print(data.decode('utf-8'))

s.close()
```

**Answer:** `f8fa738ae384ee76751805dd6829061b`

---

## Question 10 - Datagram Socket Message Sender

**Prompt:**
Gorgan Forces have requested you get a message to one of their remote teams that are utilizing the INTERNET_HOST. Utilizing the criteria they provided, generate a datagram socket with python3:

Coded information was placed into the video below. Listen to the words in the video.
https://youtu.be/OuK4OcMUGcg?t=67

Send to your INTERNET-HOST localhost:
- IP address = 127.0.0.1
- Port number = 10000
- Message = Disturbed (First letter capitalized)

**Steps:**
- Open nano stream.py
- Change socket type to DGRAM
- Adjust IP and port
- Change message
- Save, exit, and run the file in Python 3
- Boom -- your answer

**Python3 Script Used:**

```python
#!/usr/bin/python3
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

ipaddr = '127.0.0.1'
port = 10000

s.connect((ipaddr, port))
s.send(b'Disturbed')

data, conn = s.recvfrom(1024)
print(data.decode('utf-8'))

s.close()
```

**Answer:** `c474ddb8e189a2b316a8078e74e7be80`

---

## Question 11 - Raw IP Packet Sender (ipraw.py)

**Prompt:**
Use the `ipraw.py` script provided in class and modify the required fields "as needed".

- Source IP: 10.10.0.40
- Target IP: 172.16.1.15
- DSCP: 24 (adjusted to 96 in binary to meet header format)
- IP ID: 1984
- Protocol: CHAOS (protocol number 16)

Use Netcat to listen on TCP port 1111 to receive the flag:

```bash
nc -lvp 1111
```

**Steps:**
- Open `ipraw.py`
- Set Source IP to `10.10.0.40`
- Set Destination IP to `172.16.1.15`
- Set DSCP to `96` (binary padded to match header structure)
- Set IP ID to `1984`
- Set Protocol to `16` (CHAOS)
- Encode your last name using `binascii.hexlify()`
- Save and run the script
- Listen with Netcat on port 1111
- Receive the flag upon successful packet delivery

**Python3 Script Used:**

```python
#!/usr/bin/python3
import socket
import sys
from struct import pack
import base64
import binascii

try:
    s = socket.socket(socket.AF_INET, socket.SOCK_RAW, socket.IPPROTO_RAW)
except socket.error as msg:
    print(msg)
    sys.exit()

packet = ''
src_ip = "10.10.0.40"
dst_ip = "172.16.1.15"

ip_ver_ihl = 69          # Version 4 and IHL 5
ip_tos = 96              # DSCP 24 padded to binary format
ip_len = 0               # Kernel fills this
ip_id = 1984
ip_frag = 0
ip_ttl = 255
ip_proto = 16            # CHAOS protocol
ip_check = 0
ip_srcadd = socket.inet_aton(src_ip)
ip_dstadd = socket.inet_aton(dst_ip)

ip_header = pack('!BBHHHBBH4s4s', ip_ver_ihl, ip_tos, ip_len, ip_id, ip_frag, ip_ttl, ip_proto, ip_check, ip_srcadd, ip_dstadd)

message = b'last_name'
hidden_msg = binascii.hexlify(message)

packet = ip_header + hidden_msg
s.sendto(packet, (dst_ip, 0))
```

**Answer:** `3abda5f1add26199e12d0d912ff5d6a1`

---

## Question 12 - Raw IPv4 TCP Sockets (tcpraw.py)

**Prompt:**
Use the tcpraw.py script provided in class and modify the required fields "as needed".

- Source IP: 10.10.0.40
- Target IP: 172.16.1.15
- IP ID: 2020
- TCP Src port: 54321
- TCP Dst port: 1234
- SEQ Number: 90210
- ACK Number: 30905
- TCP flag: SYN

Use Netcat to listen on TCP port 1111 to receive the flag:

```bash
nc -lvp 1111
```

**Steps:**
- Open tcpraw.py
- Set Source IP to 10.10.0.40
- Set Destination IP to 172.16.1.15
- Set IP ID to 2020
- Set TCP Source Port to 54321
- Set TCP Destination Port to 1234
- Set Sequence Number to 90210
- Set ACK Number to 30905
- Set TCP Flag to SYN
- Encode last name using base64
- Save and run the script
- Listen with Netcat on port 1111
- Receive the flag

**Python3 Script Used:**

```python
#!/usr/bin/python3
import socket
import sys
import array
from struct import pack
import base64
import binascii

try:
    s = socket.socket(socket.AF_INET, socket.SOCK_RAW, socket.IPPROTO_RAW)
except socket.error as msg:
    print(msg)
    sys.exit()

src_ip = "10.10.0.40"
dst_ip = "172.16.1.15"

ip_ver_ihl = 69
ip_tos = 0
ip_len = 0
ip_id = 2020
ip_frag = 0
ip_ttl = 255
ip_proto = 6
ip_check = 0
ip_srcadd = socket.inet_aton(src_ip)
ip_dstadd = socket.inet_aton(dst_ip)

ip_header = pack('!BBHHHBBH4s4s', ip_ver_ihl, ip_tos, ip_len, ip_id, ip_frag,
                 ip_ttl, ip_proto, ip_check, ip_srcadd, ip_dstadd)

tcp_src = 54321
tcp_dst = 1234
tcp_seq = 90210
tcp_ack_seq = 30905
tcp_data_off = 5
tcp_reserve = 0
tcp_off_res = (tcp_data_off << 4) + tcp_reserve

tcp_fin = 0
tcp_syn = 1
tcp_rst = 0
tcp_psh = 0
tcp_ack = 0
tcp_urg = 0
tcp_ece = 0
tcp_cwr = 0
tcp_flags = tcp_fin + (tcp_syn << 1) + (tcp_rst << 2) + (tcp_psh << 3) + \
             (tcp_ack << 4) + (tcp_urg << 5) + (tcp_ece << 6) + (tcp_cwr << 7)

tcp_win = 5840
tcp_chk = 0
tcp_urg_ptr = 0

tcp_hdr = pack('!HHLLBBHHH', tcp_src, tcp_dst, tcp_seq, tcp_ack_seq,
               tcp_off_res, tcp_flags, tcp_win, tcp_chk, tcp_urg_ptr)

message = b'last_name'
hidden_msg = base64.b64encode(message)

src_address = socket.inet_aton(src_ip)
dst_address = socket.inet_aton(dst_ip)
reserved = 0
protocol = socket.IPPROTO_TCP
tcp_length = len(tcp_hdr) + len(hidden_msg)

ps_hdr = pack('!4s4sBBH', src_address, dst_address, reserved, protocol, tcp_length)
ps_hdr = ps_hdr + tcp_hdr + hidden_msg

def checksum(data):
    if len(data) % 2 != 0:
        data += b'\0'
    res = sum(array.array("H", data))
    res = (res >> 16) + (res & 0xffff)
    res += res >> 16
    return (~res) & 0xffff

tcp_chk = checksum(ps_hdr)

tcp_hdr = pack('!HHLLBBH', tcp_src, tcp_dst, tcp_seq, tcp_ack_seq,
               tcp_off_res, tcp_flags, tcp_win) + pack('H', tcp_chk) + pack('!H', tcp_urg_ptr)

packet = ip_header + tcp_hdr + hidden_msg
s.sendto(packet, (dst_ip, 0))
```

**Answer:** `38c18da58b334968b518141a1e4b51dc`
