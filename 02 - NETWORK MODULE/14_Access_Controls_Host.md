# Access Controls -- Host

## Why Filter Traffic

- Block malicious traffic
- Decrease load on network infrastructure
- Ensure efficient data flow
- Ensure data reaches only intended recipients
- Obfuscate network internals

---

## Traffic Filtering Methods & Technologies

| Method | Description |
|---|---|
| Network Traffic Filtering | Allow/block traffic to/from remote locations (e.g., firewall rules) |
| Email Filtering | Block spam, phishing, or malicious email |
| Application Filtering | Restrict vulnerable or unauthorized software from running |
| MAC Filtering | Allow only specific devices (via MAC address) to access the network |

---

## Network Traffic Filtering Concepts

| Concept | Description |
|---|---|
| Protocols Operation | Understanding protocol behaviors to distinguish normal vs. malicious activity |
| Header Analysis | Inspecting packet headers (source/destination, flags, anomalies) |
| Network Recon | Detecting scans, probes, or enumeration attempts |
| Tunnel Analysis | Identifying hidden traffic in VPNs, proxies, or encrypted tunnels |
| IOA & IOC | Indicators of Attack (tactics/behaviors) and Indicators of Compromise (artifacts) |
| Malware Analysis | Examining suspicious traffic for malware delivery or C2 communication |

---

## Defense in Depth

| Layer | Description |
|---|---|
| Perimeter Security | Firewalls, IDS/IPS, network gateways |
| Network Security | Segmentation, filtering, continuous monitoring |
| Endpoint Security | Antivirus, EDR, patch management |
| Application/OS Sec. | Secure coding, regular updates, access control enforcement |
| Data Security | Encryption, DLP (Data Loss Prevention), strict access controls |

---

## Filtering Devices, Mechanisms, and OSI Operation Layers

| Device | Filtering Mechanisms | Operates at OSI Layer |
|---|---|---|
| Switch | PACL (Port ACL), VACL (VLAN ACL), ACL | Layer 2, Layer 3 |
| Router | ACL | Layers 3 & 4 |
| Proxies | Content-based filtering: URL/DNS blacklists, MIME filtering, keyword checks | Layers 3-7 |
| Intrusion Detection/Prevention | Signatures | Layers 3-7 |
| Host-Based Firewall | Rules | Layers 3-7 |
| Network Firewall | Rules, Packet Filtering (stateless), Stateful, Application Layer FW, NGFW | Layers 3-7 |

---

## Firewall Operation Modes

### Routed Mode (Layer 3)
- Operates at OSI Layer 3
- Firewall functions as a router
- Each interface belongs to its own IP subnet
- Provides routing between networks
- Commonly used at enterprise network edges

### Transparent Mode (Layer 2)
- Operates at OSI Layer 2
- Firewall functions as a bridge/switch
- Interfaces are in the same IP subnet
- Does not alter IP addressing (appears "invisible")
- Ideal for inline deployments without readdressing

---

## Default Policies

| Policy Type | Description |
|---|---|
| Explicit | Precisely and clearly expressed rule (administrator-defined) |
| Implicit | Implied or understood action (default system behavior) |

### Block-Listing vs Allow-Listing

| Type | Implicit Behavior | Explicit Behavior | Notes |
|---|---|---|---|
| Block-Listing | Implicit ACCEPT | Explicit DENY | Block known bad |
| Allow-Listing | Implicit DENY | Explicit ACCEPT | Allow only known good |

---

## Firewall Filtering Methods

- Stateless (Packet) Filtering (L3+4)
- Stateful Inspection (L4)
- Circuit-Level (L5)
- Application Layer (L7)
- Next Generation (NGFW) (L7)

---

## Software vs Hardware vs Cloud Firewalls

| Type | Description |
|---|---|
| Software | Typically host-based |
| Hardware | Typically network-based |
| Cloud | Provided as a service |

---

## Traffic Directions

### A to B
- Traffic originating from the localhost to the remote-host (you are the client)
- Return traffic from that remote-host back to the localhost (server responding)

### B to A
- Traffic originating from the remote-host to the localhost (client connecting to you)
- Return traffic from the localhost back to the remote-host (you responding as server)

---

## Host-Based Filtering

### Windows
- Norton, McAfee, ZoneAlarm, Avast, Windows Defender Firewall

### Linux
- iptables, nftables, UFW (Uncomplicated Firewall), firewalld

### MAC
- Little Snitch, LuLu, Vallum

### Configuration Example (iptables)

```bash
# Block incoming traffic from a specific IP
iptables -A INPUT -s 192.168.1.100 -j DROP

# Allow SSH access from a trusted IP
iptables -A INPUT -p tcp --dport 22 -s 192.168.1.50 -j ACCEPT

# Drop all other incoming traffic
iptables -A INPUT -j DROP
```

---

## Netfilter Framework

Built into the Linux kernel to support advanced packet processing and firewall capabilities.

**Provides:**
- Packet filtering
- Stateless and stateful firewalling
- Network Address Translation (NAT) and Port Address Translation (PAT)
- Other packet manipulation functions

### Netfilter Hooks and Corresponding Chains

| Hook | Chain | Description |
|---|---|---|
| NF_IP_PRE_ROUTING | PREROUTING | Before routing decision |
| NF_IP_LOCAL_IN | INPUT | Packets destined for the local system |
| NF_IP_FORWARD | FORWARD | Packets being routed through the system |
| NF_IP_LOCAL_OUT | OUTPUT | Packets originating from the local system |
| NF_IP_POST_ROUTING | POSTROUTING | After routing decision, before leaving system |

### Netfilter Paradigm

- **Tables:** Contain chains
- **Chains:** Contain rules
- **Rules:** Dictate what to match and what actions to perform on packets

### Netfilter Applications by Protocol

| Application | Description |
|---|---|
| iptables | IPv4 packet administration |
| ip6tables | IPv6 packet administration |
| ebtables | Ethernet bridge frame table administration |
| arptables | ARP packet administration |

---

## Configure iptables Filtering Rules

### Tables and Chains

**Tables of iptables:**
- **filter:** Default table. Provides packet filtering.
- **nat:** Used to translate private <-> public address and ports.
- **mangle:** Provides special packet alteration. Can modify various header fields.
- **raw:** Used to configure exemptions from connection tracking.
- **security:** Used for Mandatory Access Control (MAC) networking rules.

**Chains of iptables:**
- PREROUTING: packets entering NIC before routing
- INPUT: packets to localhost after routing
- FORWARD: packets routed from one NIC to another
- OUTPUT: packets from localhost to be routed
- POSTROUTING: packets leaving system after routing

**Chains assigned to each Table:**
- filter: INPUT, FORWARD, OUTPUT
- nat: PREROUTING, POSTROUTING, INPUT, OUTPUT
- mangle: All chains
- raw: PREROUTING, OUTPUT
- security: INPUT, FORWARD, OUTPUT

---

## iptables Syntax and Options

### Command Syntax

```bash
iptables -t [table] -A [chain] [rules] -j [action]
```

### Common Options (Rule Management)

| Option | Description |
|---|---|
| `-t` | Specifies the table (default is filter) |
| `-A` | Appends a rule to the end of the list |
| `-I` | Inserts the rule at the top of the list or above specified rule |
| `-R` | Replaces a rule at the specified rule number |
| `-D` | Deletes a rule at the specified rule number |
| `-F` | Flushes the rules in the selected chain |
| `-L` | Lists the rules in the selected chain using standard formatting |
| `-S` | Lists the rules in the selected chain without standard formatting |
| `-P` | Sets the default policy for the selected chain |
| `-n` | Disables inverse lookups when listing rules |
| `--line-numbers` | Prints the rule number when listing rules |

### Common Options (Rule Matching)

| Option | Description |
|---|---|
| `-p` | Specifies the protocol |
| `-i` | Specifies the input interface |
| `-o` | Specifies the output interface |
| `--sport` | Specifies the source port |
| `--dport` | Specifies the destination port |
| `-s` | Specifies the source IP |
| `-d` | Specifies the destination IP |
| `-j` | Specifies the jump target action |

### Protocol-Specific Syntax

```bash
-p icmp [--icmp-type type# {/code#}]
-p tcp [--sport | --dport {port1 | port1:port2}]
-p tcp [--tcp-flags SYN,ACK,PSH,RST,FIN,URG,ALL,NONE]
-p udp [--sport | --dport {port1 | port1:port2}]
```

---

## iptables Match Extensions and Actions

### Match Extensions (-m)

```bash
# Stateful packet inspection
-m state --state [NEW, ESTABLISHED, RELATED, UNTRACKED, INVALID]

# MAC address matching
-m mac [--mac-source | --mac-destination] [MAC address]

# Multiple ports in a single rule
-p [tcp|udp] -m multiport [--dports | --sports | --ports {port1 | port1:port15}]

# Berkeley Packet Filter bytecode
-m bpf --bytecode ['bytecode']

# IP range matching
-m iprange [--src-range | --dst-range {ip1-ip2}]
```

### Actions (-j)

| Action | Description |
|---|---|
| ACCEPT | Allow the packet |
| REJECT | Deny the packet and send an ICMP response |
| DROP | Silently deny the packet (no response) |

---

## Modifying iptables Rules

```bash
# Flush table
iptables -t [table] -F

# Change default policy
iptables -t [table] -P [chain] [action]

# List rules with rule numbers
iptables -t [table] -L --line-numbers

# List rules as interpreted commands
iptables -t [table] -S

# Insert rule before rule number
iptables -t [table] -I [chain] [rule num] [rules] -j [action]

# Replace rule at rule number
iptables -t [table] -R [chain] [rule num] [rules] -j [action]

# Delete rule at rule number
iptables -t [table] -D [chain] [rule num]
```

---

## NFTables Configuration

### NAT Configuration

```bash
# Create NAT table
nft add table ip NAT

# Create NAT chains
nft add chain ip NAT PREROUTING { type nat hook prerouting priority 0 \; }
nft add chain ip NAT POSTROUTING { type nat hook postrouting priority 0 \; }

# Source NAT
nft add rule ip NAT POSTROUTING ip saddr 10.10.0.40 oif eth0 snat 144.15.60.11
nft add rule ip NAT POSTROUTING oif eth0 masquerade

# Destination NAT
nft add rule ip NAT PREROUTING iif eth0 ip daddr 144.15.60.11 dnat 10.10.0.40
nft add rule ip NAT PREROUTING iif eth0 tcp dport { 80, 443 } dnat 10.1.0.3
nft add rule ip NAT PREROUTING iif eth0 tcp dport 80 redirect to 8080
```

### Mangle Rules with iptables

```bash
# TTL modification
iptables -t mangle -A POSTROUTING -o eth0 -j TTL --ttl-set 128

# DSCP tagging
iptables -t mangle -A POSTROUTING -o eth0 -j DSCP --set-dscp 26
```

---

## NFTables Enhancements

**Unified Command:** `nft` replaces iptables, ip6tables, arptables, and ebtables.

**Improvements:**
- Cleaner, more consistent rule syntax
- Less code duplication, faster rule execution
- Dual-stack support (IPv4 and IPv6 simultaneously)

### NFTables Families

| Family | Description | Equivalent |
|---|---|---|
| ip | Handles IPv4 packets | iptables |
| ip6 | Handles IPv6 packets | ip6tables |
| inet | Supports both IPv4 and IPv6 | Unified rules |
| arp | Operates at Layer 2, manages ARP traffic | arptables |
| bridge | Processes packets traversing Linux bridges | ebtables |
| netdev | Hooks into ingress path before networking stack | No equivalent |

### NFTables Hooks

| Hook | Description |
|---|---|
| ingress | netdev only; captures packets before entering the stack |
| prerouting | Before routing decisions; used for DNAT and marking |
| input | For packets destined to the local system |
| forward | For packets being routed through the system |
| output | For packets originating from the local system |
| postrouting | After routing decisions; used for SNAT and masquerading |

### NFTables Chain Types

| Type | Purpose | Supported Families |
|---|---|---|
| filter | Packet filtering | arp, bridge, ip, ip6, inet |
| route | Packet rerouting | ip, ip6 |
| nat | SNAT, DNAT, masquerading | ip, ip6 |

---

## NFTables Syntax

### Creating Tables

```bash
nft add table [family] [table]
```

### Creating Base Chains

```bash
nft add chain [family] [table] [chain] { type [type] hook [hook] \
    priority [priority] \; policy [policy] \;}
```

### Creating Rules

```bash
nft add rule [family] [table] [chain] [matches] [statement]
```

### Rule Match Options -- Protocol Specific

```bash
# IP address matching
ip [ saddr | daddr { ip | ip1-ip2 | ip/CIDR | ip1, ip2, ip3 } ]

# TCP matching
tcp flags { syn, ack, psh, rst, fin }
tcp [ sport | dport { port1 | port1-port2 | port1, port2, port3 } ]

# UDP matching
udp [ sport | dport { port1 | port1-port2 | port1, port2, port3 } ]

# ICMP matching
icmp [ type | code { type# | code# } ]

# Connection tracking
ct state { new, established, related, invalid, untracked }

# Interface matching
iif [iface]
oif [iface]
```

### Ruleset and Table Management

```bash
# Ruleset management
nft list ruleset
nft flush ruleset

# Table management
nft delete table [family] [table]
nft list table [family] [table]
nft flush table [family] [table]

# Chain management
nft delete chain [family] [table] [chain]
nft list chain [family] [table] [chain]
nft flush chain [family] [table] [chain]
```

### Rule Positioning

```bash
# List table with handles
nft list table [family] [table] -a

# Add after position
nft add rule [family] [table] [chain] position <position> [matches] [statement]

# Insert before position
nft insert rule [family] [table] [chain] position <position> [matches] [statement]

# Replace rule at handle
nft replace rule [family] [table] [chain] handle <handle> [matches] [statement]

# Delete rule at handle
nft delete rule [family] [table] [chain] handle <handle>
```

### Change Chain Policy

```bash
nft add chain [family] [table] [chain] { \; policy [policy] \;}
```

---

## Configure iptables NAT Rules

### NAT & PAT Operators and Chains

| Operator | Description | Valid Chain |
|---|---|---|
| SNAT | Modifies source IP of outgoing packets | POSTROUTING |
| MASQUERADE | SNAT using outgoing interface's IP (dynamic IPs) | POSTROUTING |
| DNAT | Modifies destination IP of incoming packets | PREROUTING, OUTPUT |
| REDIRECT | DNAT that redirects traffic to localhost | PREROUTING |

### Source NAT Examples

```bash
# Change source IP to static address
iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to 1.1.1.1

# Change source IP and port for TCP
iptables -t nat -A POSTROUTING -p tcp -o eth0 -j SNAT --to 1.1.1.1:9001

# Use MASQUERADE for dynamic IP
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```

### Destination NAT Examples

```bash
# Change destination IP for all traffic
iptables -t nat -A PREROUTING -i eth0 -j DNAT --to 10.0.0.1

# Match specific ports and redirect
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 22 -j DNAT --to 10.0.0.1:22
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j DNAT --to 10.0.0.2:80
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 443 -j DNAT --to 10.0.0.3:443

# Redirect to localhost port
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j REDIRECT --to-port 8080
```

---

## Configure Mangle Rules

### iptables Mangle

```bash
# Set TTL for outgoing traffic
iptables -t mangle -A POSTROUTING -o eth0 -j TTL --ttl-set 128

# Set DSCP for outgoing traffic
iptables -t mangle -A POSTROUTING -o eth0 -j DSCP --set-dscp 26
```

### nftables Mangle

```bash
# Create MANGLE table
nft add table ip MANGLE

# Create MANGLE chains
nft add chain ip MANGLE INPUT {type filter hook input priority 0 \; policy accept \;}
nft add chain ip MANGLE OUTPUT {type filter hook output priority 0 \; policy accept \;}

# Set TTL for outgoing traffic
nft add rule ip MANGLE OUTPUT oif eth0 ip ttl set 128

# Set DSCP for outgoing traffic
nft add rule ip MANGLE OUTPUT oif eth0 ip dscp set 26
```
