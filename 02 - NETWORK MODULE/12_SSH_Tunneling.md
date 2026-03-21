# SSH Tunneling and Covert Channels

## Overview

**Key Outcomes:**
- Understand and demonstrate Local, Dynamic, and Remote SSH Port Forwarding

**Tunneling Basics:**
- Encapsulates one protocol inside another
- Phases: Encapsulation -> Transmission -> Decapsulation

---

## IPv6 over IPv4 (RFC 3056)

- Enables IPv6 traffic over IPv4 networks
- Uses Dual Stack Routers
- Payloads may be unencrypted unless secured with IPSEC

**Use Cases:**
- IPv4 to IPv6 migration
- Malware evasion and covert channels

---

## SSH Port Forwarding Techniques

### 1. Local Port Forwarding

Redirects local traffic to remote service.

```bash
ssh -L 8080:internal.server.com:80 user@relay.host
```

### 2. Dynamic Port Forwarding

Creates a SOCKS proxy for flexible routing.

```bash
ssh -D 1080 user@proxy.host
```

### 3. Remote Port Forwarding

Exposes local service to remote host.

```bash
ssh -R 9090:localhost:22 user@remote.host
```

---

## Covert Channel Risks

- SSH tunnels used for hidden data exfiltration
- Encrypted traffic can mask C2 activity
- Bypasses firewalls and perimeter defenses

**Defensive Tips:**
- Monitor SSH usage and port forwarding
- Use deep packet inspection
- Disable SSH forwarding where not needed

---

## Tunneling Protocols

### 1. Dual Stack

- Configures both IPv4 and IPv6 addresses on all devices
- Allows simultaneous routing of IPv4 and IPv6 traffic
- Resource intensive due to dual protocol management

```
IPv6 Host (Left)                IPv6 Host (Right)
      |                               |
      | IPv6                          | IPv6
      v                               v
[Dual Stack Router A]         [Dual Stack Router B]
  IPv4: 192.168.10.10           IPv4: 192.168.16.10
  IPv6: 2002:0A8A:0A0A::/48    IPv6: 2002:0A8A:100A::/48
      |                               |
      | Encapsulate IPv6 -> IPv4       | Decapsulate IPv4 -> IPv6
      v                               v
      ======== 6to4 Tunnel (IPv6 over IPv4 Internet) ========
```

### 2. 6in4

- Encapsulates IPv6 packets inside IPv4 using protocol 41
- Requires static configuration
- Often used in point-to-point tunnels

### 3. 6to4

- Automatic tunneling method for IPv6 over IPv4
- Uses `2002::/16` prefix derived from IPv4 address
- No manual configuration needed

### 4. 4in6

- Encapsulates IPv4 packets inside IPv6
- Uses Next Header value 4
- Less common, used in specialized scenarios

### 5. Teredo Tunneling

- Defined in RFC 4380
- Allows IPv4 clients to access IPv6 networks
- Encapsulates IPv6 packets within UDP
- Designed for NAT traversal
- Uses prefix: `2001:0000::/32`

### 6. ISATAP (Intra-Site Automatic Tunnel Addressing Protocol)

- Enables IPv6 communication over IPv4 within a local site
- Generates link-local IPv6 address using IPv4
- Example: IPv4 `192.168.199.99` -> IPv6 `FE80::0000:5EFE:c0a8:c763`

---

## Covert Channels vs Steganography

### Covert Channels

- Hidden communication paths within legitimate protocols
- Used to bypass security controls or exfiltrate data
- Examples: DNS tunneling, ICMP payload abuse, timing channels

### Steganography

- Hides data within digital media (images, audio, text)
- Goal is concealment, not encryption
- Often used in malware, espionage, and C2 operations

### Steganography Techniques

**1. Injection**
- Inserts message into unused space (e.g., whitespace in image files)
- Adds file size, hard to detect without original

```bash
steghide extract -sf image.jpg
```

**2. Substitution**
- Replaces insignificant data (e.g., pixel values, audio bits)
- Most common method
- Example: Modify color palette +/-1 to encode message

**3. Propagation**
- Generates a new file with embedded message
- Tools: StegSecret, HyDEn, Spammimic

---

## SSH Components and Key Roles

### SSH Architecture

- **Client:** Initiates the connection
- **Server:** Accepts and manages incoming SSH requests
- **Session:** Encrypted communication channel between client and server

### Key Types

| Key Type | Description |
|---|---|
| User Key | Asymmetric public key used to authenticate user to server |
| Host Key | Asymmetric public key used to verify server identity to client |
| Session Key | Symmetric key generated during handshake to encrypt session traffic |

### Configuration Files

| File | Purpose |
|---|---|
| `/etc/ssh/ssh_config` | Controls client-side SSH behavior |
| `/etc/ssh/sshd_config` | Defines server-side SSH settings and access rules |
| `~/.ssh/known_hosts` | Stores trusted server fingerprints for client verification |

---

## SSH Connection Behavior

### First-Time Connection

```bash
ssh student@172.16.82.106
```

- Host key is saved to `~/.ssh/known_hosts`
- Future connections skip key prompt unless host key changes

### Host Key Change Warning

Occurs when server's key differs from saved fingerprint. Possible causes: server reinstalled, man-in-the-middle attack.

**Resolution:**

```bash
ssh-keygen -f "~/.ssh/known_hosts" -R "172.16.82.106"
```

Then reconnect to accept new key.

---

## SSH Port Forwarding Overview

Uses SSH-CONN protocol to create encrypted tunnels. Enables secure access to otherwise insecure services.

### SSH Options Summary

| Option | Description |
|---|---|
| `-L` | Local Port Forwarding -- Maps local port to remote ip:port via SSH server |
| `-D` | Dynamic Port Forwarding -- Sets up SOCKS proxy; target ip:port chosen dynamically |
| `-R` | Remote Port Forwarding -- Maps server port to local ip:port via SSH client |
| `-NT` | No remote command, no pseudo-tty -- Used for silent tunnel setups |

### Local Port Forwarding Syntax

```bash
ssh -p <alt port> <user>@<server ip> -L <local port>:<target ip>:<target port>
# or
ssh -L <local port>:<target ip>:<target port> -p <alt port> <user>@<server ip>
```

---

## Bridging Local and Remote Port Forwarding

### Chaining Tunnels

**Step 1 -- Local Port Forwarding to Internal Host:**

```bash
ssh student@172.16.1.15 -L 2222:localhost:1122
```

Effect: Binds port 2222 on Internet_Host to port 1122 on Internal_Host.

**Step 2 -- Dynamic Port Forwarding over Local Tunnel:**

```bash
ssh student@localhost -p 2222 -D 9050 2>/dev/null
```

Effect: Creates SOCKS proxy on port 9050 through the chained tunnel.

---

## Multi-Hop Tunnel Example

**Step 1 -- Local Port Forwarding to Telnet Target:**

```bash
ssh student@172.16.1.15 -L 2223:172.16.40.10:23 -NT
```

Access via: `telnet localhost 2223`

**Step 2 -- Remote Port Forwarding from Internal Host:**

```bash
ssh student@172.16.1.15 -R 1122:localhost:22 -NT
```

Port 1122 on Blue_DMZ_Host-1 forwards to port 22 on Blue_INT_DMZ_Host-1.

**Step 3 -- Local Port Forwarding to Remote Port:**

```bash
ssh student@172.16.1.15 -L 2222:localhost:1122 -NT
```

Port 2222 on Internet_Host links to port 22 on Blue_INT_DMZ_Host-1.

**Step 4 -- Dynamic Port Forwarding via Chained Tunnel:**

```bash
ssh student@localhost -p 2222 -D 9050 2>/dev/null
```

SOCKS proxy on port 9050 routes traffic through full tunnel chain.

---

## Proxychains Usage Examples

```bash
proxychains ./scan.sh
proxychains nmap -Pn -sT 172.16.82.96/27 -p 21-23,80
proxychains ssh student@172.16.82.106
proxychains telnet 172.16.82.106
proxychains wget -r http://172.16.82.106
proxychains wget -r ftp://172.16.82.106
```

---

## SSH Command Breakdown Templates

### Local Port Forwarding

```bash
ssh -L <local_port>:<target_ip>:<target_port> <user>@<server_ip> -NT
#     |                                       |                   |
#   option        local port forwarding     ssh auth            flags
```

### Remote Port Forwarding

```bash
ssh -R <remote_port>:<target_ip>:<target_port> <user>@<server_ip> -NT
#     |                                        |                   |
#   option        remote port forwarding      ssh auth            flags
```

### Dynamic Port Forwarding

```bash
ssh -D <local_port> <user>@<server_ip> 2>/dev/null
#     |             |                   |
#   SOCKS proxy   ssh auth           suppress output
```
