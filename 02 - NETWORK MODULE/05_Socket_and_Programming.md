# Socket and Programming

## Packet Creation and Socket Programming

**Objectives:**
- Identify Socket Types for Network Functions
- Discuss Network Programming with Python3
- Demonstrate Stream Socket Programming
- Demonstrate Datagram Socket Programming
- Demonstrate RAW Socket with Python3
- Demonstrate RAW TCP Socket with Python3

---

## Socket Types

- **Stream Socket** -- Connection-oriented and sequenced; used with TCP, SCTP, and Bluetooth.
- **Datagram Socket** -- Connectionless and fast; used with UDP for quick data exchange.
- **RAW Socket** -- Direct access to IP packets; used for crafting or inspecting custom protocol headers.

---

## Network Communication Layers

- **Network Applications** -- Operate in user space; initiate and manage network communication.
- **BSD Sockets** -- Interface between user applications and kernel-level protocols.
- **INET Sockets** -- Kernel-level socket layer connecting to transport protocols.
- **TCP / UDP** -- Transport layer protocols; TCP is connection-oriented, UDP is connectionless.
- **IP** -- Internet Protocol; handles addressing and routing of packets.
- **PPP / SLIP / Ethernet** -- Data link layer protocols interfacing with physical network hardware.

---

## Kernel Space Applications / Sockets

### Scapy

Python-based tool used to craft, modify, and transmit custom packets at various protocol layers.

```python
from scapy.all import IP, ICMP, send
packet = IP(dst="8.8.8.8")/ICMP()
send(packet)
```

### Python RAW Sockets

Enable direct packet manipulation and transmission using low-level socket APIs.

```python
import socket
s = socket.socket(socket.AF_INET, socket.SOCK_RAW, socket.IPPROTO_ICMP)
s.sendto(b'\x08\x00\x00\x00', ("8.8.8.8", 0))
```

### Routing Protocols

Network devices use protocols like OSPF to dynamically exchange routing information. OSPF is implemented in routers using software like Quagga or FRRouting to manage route tables.

### ICMP Traffic

Operates without a transport layer header; used for diagnostics (e.g., ping, traceroute).

---

## Python3 Socket Creation

```python
import socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM, socket.IPPROTO_TCP)

# Changeable Parameters:
# socket.AF_INET        - Use for IPv4
# socket.AF_INET6       - Use for IPv6
# socket.SOCK_STREAM    - Use for TCP
# socket.SOCK_DGRAM     - Use for UDP
# socket.IPPROTO_TCP    - TCP Protocol
# socket.IPPROTO_UDP    - UDP Protocol

# Example Usage:
s.connect(("example.com", 80))
s.send(b"GET / HTTP/1.1\r\nHost: example.com\r\n\r\n")
response = s.recv(4096)
print(response)
```

---

## socket.socket() Arguments

```python
socket.socket(family, type, proto)
```

**family** -- Defines the address family:
- `AF_INET` -- IPv4 (default)
- `AF_INET6` -- IPv6
- `AF_UNIX` -- Local inter-process communication

**type** -- Defines the socket type:
- `SOCK_STREAM` -- Stream socket (TCP)
- `SOCK_DGRAM` -- Datagram socket (UDP)
- `SOCK_RAW` -- RAW socket for custom packet crafting

**proto** -- Defines the protocol used:
- `0` -- Default protocol for selected type
- `IPPROTO_TCP` -- TCP
- `IPPROTO_UDP` -- UDP
- `IPPROTO_IP` -- IP
- `IPPROTO_ICMP` -- ICMP
- `IPPROTO_RAW` -- RAW IP packets

---

## socket.setsockopt()

Used to configure socket options at runtime.

```python
import socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
```

**Parameters:**
- `socket.SOL_SOCKET` -- Level at which the option is defined (socket level).
- `socket.SO_REUSEADDR` -- Allows the socket to bind to an address that is in a TIME_WAIT state.
- `1` -- Enables the option (use 0 to disable).

---

## Python3 Socket Operations

### Server Operations

```python
# Configure socket options
s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)

# Bind server socket to IP and port
s.bind(('', 12345))  # '' binds to all available interfaces

# Prepare server to accept incoming connections
s.listen()

# Accept incoming TCP connection from client
conn, addr = s.accept()  # Returns new socket object and client address
```

### Client Operations

```python
# Client initiates TCP connection to server
s.connect(('127.0.0.1', 12345))
```

### Sending Data

```python
# Send data over TCP connection
s.send(b"Hello World")

# Ensure all data is sent over TCP
s.sendall(b"Hello World")

# Send data over UDP to specific address
s.sendto(b"Hello World", ('172.16.0.1', 12345))
```

### Receiving Data

```python
# Receive data from TCP socket
data = s.recv(1024)

# Receive data and sender address (UDP)
data, addr = s.recvfrom(1024)
```

### Closing

```python
s.close()  # Gracefully shuts down socket
```

---

## Stream Socket Sender Demo

```python
#!/usr/bin/python3
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM, 0)
ip_addr = '127.0.0.1'
port = 1111

s.connect((ip_addr, port))
message = b"Message"
s.send(message)

data, conn = s.recvfrom(1024)
print(data.decode('utf-8'))

s.close()
```

---

## Stream Socket Receiver Demo

```python
#!/usr/bin/python3
import socket
import os

port = 1111
message = b"Connected to TCP Server on port %i\n" % port

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM, 0)
s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
s.bind(('', port))
s.listen(1)

os.system("clear")
print("Waiting for TCP connections\n")

while 1:
    conn, addr = s.accept()
    connect = conn.recv(1024)
    address, port = addr
    print("Message Received - '%s'" % connect.decode())
    print("Sent by -", address, "port -", port, "\n")
    conn.sendall(message)
    conn.close()
```

---

## Datagram (UDP) Socket Sender Demo

```python
#!/usr/bin/python3
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
ip_addr = '127.0.0.1'
port = 2222

message = b"Hello via UDP"
s.sendto(message, (ip_addr, port))

response, addr = s.recvfrom(1024)
print("Server Response:", response.decode('utf-8'))

s.close()
```

---

## Datagram (UDP) Socket Receiver Demo

```python
#!/usr/bin/python3
import socket
import os

port = 2222
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM, 0)
s.bind(('', port))

os.system("clear")
print("Awaiting UDP Messages\n")

while True:
    data, addr = s.recvfrom(1024)
    address, port = addr
    print("Message Received: '%s'" % data.decode('utf-8'))
    print("Sent by -", address, "port -", port)
    s.sendto(b"Message received by the UDP Message Server!", addr)
```

---

## Encoding vs Encryption

### Encoding
- Converts data into a different format for compatibility or transmission.
- Not designed for confidentiality.
- Common Types: Base64, ASCII, UTF-8 / UTF-16 / Unicode, URL encoding

### Encryption
- Scrambles data to make it unreadable without a secret key.
- Requires decryption key to restore original data.
- Common Algorithms: AES, RSA, Blowfish, ChaCha20

### Key Differences
- Encoding is reversible without a key
- Encryption is reversible only with the correct key
- Encoding ensures data integrity across systems
- Encryption ensures data confidentiality

---

## Hex Encoding and Decoding

### CLI

```bash
# Encode text to Hex
echo "Message" | xxd -p
# Outputs: 4d657373616765

# Encode file to Hex
xxd file.txt file-encoded.txt

# Decode file from Hex
xxd -r file-encoded.txt file-decoded.txt
```

### Python

```python
import binascii
message = b'Message'
hidden_msg = binascii.hexlify(message)
new_msg = binascii.unhexlify(hidden_msg)
print(new_msg.decode('utf-8'))
```

---

## Base64 Encoding and Decoding

### CLI

```bash
# Encode text to Base64
echo "Message" | base64
# Outputs: TWVzc2FnZQo=

# Encode file to Base64
base64 file.txt > file-encoded.txt

# Decode file from Base64
base64 -d file-encoded.txt > file-decoded.txt
```

### Python

```python
import base64
message = b'Message'
hidden_msg = base64.b64encode(message)
new_msg = base64.b64decode(hidden_msg)
print(new_msg.decode('utf-8'))
```
