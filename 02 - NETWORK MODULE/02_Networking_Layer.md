# Networking Layer

## IPv4 Address Classes -- Summary

IPv4 originally defined five address classes (A-E), each serving different network sizes and purposes. These are identified by the first few bits of the IP address.

### Class A

- **Range:** 1.0.0.0 to 126.255.255.255
- **CIDR Notation:** /8
- **Subnet Mask:** 255.0.0.0
- **Hosts per Network:** ~16 million
- **Use:** Very large networks
- **Binary Prefix:** `0xxxxxxx`
- **Binary Range:**
  - Start: `00000000.00000000.00000000.00000000` (0.0.0.0)
  - End: `01111111.11111111.11111111.11111111` (127.255.255.255)
- **Example:** 10.0.0.1 = `00001010.00000000.00000000.00000001`

### Class B

- **Range:** 128.0.0.0 to 191.255.255.255
- **CIDR Notation:** /16
- **Subnet Mask:** 255.255.0.0
- **Hosts per Network:** ~65,000
- **Use:** Medium-sized networks
- **Binary Prefix:** `10xxxxxx`
- **Binary Range:**
  - Start: `10000000.00000000.00000000.00000000` (128.0.0.0)
  - End: `10111111.11111111.11111111.11111111` (191.255.255.255)
- **Example:** 172.16.0.1 = `10101100.00010000.00000000.00000001`

### Class C

- **Range:** 192.0.0.0 to 223.255.255.255
- **CIDR Notation:** /24
- **Subnet Mask:** 255.255.255.0
- **Hosts per Network:** 254
- **Use:** Small networks
- **Binary Prefix:** `110xxxxx`
- **Binary Range:**
  - Start: `11000000.00000000.00000000.00000000` (192.0.0.0)
  - End: `11011111.11111111.11111111.11111111` (223.255.255.255)
- **Example:** 192.168.1.1 = `11000000.10101000.00000001.00000001`

### Class D (Multicast)

- **Range:** 224.0.0.0 to 239.255.255.255
- **CIDR Notation:** N/A
- **Use:** Multicast group communication
- **Binary Prefix:** `1110xxxx`

### Class E (Experimental)

- **Range:** 240.0.0.0 to 255.255.255.255
- **CIDR Notation:** N/A
- **Use:** Reserved for experimental purposes
- **Binary Prefix:** `1111xxxx`

> **Note:** Modern networks use CIDR (Classless Inter-Domain Routing) instead of class-based addressing for more flexible subnetting.

---

## Reserved IPv4 Address Ranges

1. **Private IP Ranges** (Not routable on the public internet)
   - `10.0.0.0` to `10.255.255.255` (Class A)
   - `172.16.0.0` to `172.31.255.255` (Class B)
   - `192.168.0.0` to `192.168.255.255` (Class C)
   - Purpose: Used for internal LANs; requires NAT for internet access

2. **Loopback Addresses**
   - `127.0.0.0` to `127.255.255.255`
   - Purpose: Used for internal host communication (e.g., localhost testing)

3. **Link-local Automatic Private IP Addressing (APIPA)**
   - `169.254.0.0` to `169.254.255.255`
   - Purpose: Auto-assigned when DHCP fails; used for local communication only

4. **Multicast Range**
   - `224.0.0.0` to `239.255.255.255`
   - Purpose: Used for multicast traffic (e.g., streaming, routing protocols)

5. **Broadcast Address**
   - `255.255.255.255`
   - Purpose: Sends traffic to all hosts on a local network

6. **Experimental / Reserved for Future Use**
   - `240.0.0.0` to `255.255.255.254`
   - Purpose: Reserved for future or experimental use; not publicly routable

7. **Network ID / Default Route**
   - `0.0.0.0`
   - Purpose: Represents "this network" or default route in routing tables

---

## DHCP D.O.R.A Process

**D -- DHCP Discover**
- The client sends a broadcast message to locate available DHCP servers.
- Destination IP: `255.255.255.255`
- Destination MAC: `ff:ff:ff:ff:ff:ff`
- Purpose: "Who can give me an IP address?"

**O -- DHCP Offer**
- DHCP servers respond with an available IP address and configuration details.
- Includes: IP address, subnet mask, default gateway, DNS servers
- Sent as a unicast to the client's MAC address
- Purpose: "Here's what I can offer you."

**R -- DHCP Request**
- The client selects one offer and sends a request to that DHCP server.
- Includes the offered IP address
- May send requests to multiple servers but accepts only one
- Purpose: "I'd like to use this IP address."

**A -- DHCP Acknowledge (ACK)**
- The DHCP server confirms the IP assignment and reserves it for the client.
- Sends a unicast ACK message with final configuration details
- Purpose: "You're officially assigned this IP address."

> **Summary:** The D.O.R.A process ensures dynamic IP allocation and configuration for devices joining a network, enabling seamless connectivity without manual setup.

---

## TTL (Time To Live) -- Platform-Based Defaults

TTL is a field in the IP header that limits the lifespan of a packet. Each router hop decrements the TTL by 1. If TTL reaches 0, the packet is discarded. This helps prevent routing loops.

> **Key Insight:** Unless you capture the packet directly from the source host, the TTL value will likely be lower than its original default due to hop reduction.

### Typical TTL Ranges by Platform

| Platform | Default TTL | Common Observed Range |
|---|---|---|
| Linux | 64-128 | 34-64 |
| Windows | 128 | 98-128 |
| Cisco Devices | 255 | 225-255 |

**General Rule:** Most internet destinations are reachable within ~30 hops, so TTL values observed in packet captures are often 30 or more below their original default.

**Use Case:** TTL values can help identify the operating system of a remote host during network reconnaissance or troubleshooting.

---

## Traceroute (Linux) -- Quick Summary

Traceroute maps the path packets take to a destination by sending packets with increasing TTL values. Each router that drops a packet (TTL = 0) replies with an ICMP "Time Exceeded" message, revealing its IP and response time.

```bash
traceroute <destination>
```

**Common Options:**

| Option | Description |
|---|---|
| `-n` | Show numeric IPs only (skip DNS lookup) |
| `-I` | Use ICMP Echo instead of default UDP |
| `-T` | Use TCP SYN packets |
| `-m <max_ttl>` | Set max number of hops |
| `-p <port>` | Specify destination port (UDP/TCP) |

**Use Case:** Helps diagnose routing issues, latency, or unreachable nodes across networks.

---

## IPv6 Private Addressing and Class Structure

IPv6 does not use traditional address classes like IPv4 (A, B, C). Instead, it uses prefix-based allocation and address types defined by function.

### Address Types

| Address Type | Prefix | Routable | Description |
|---|---|---|---|
| Unique Local (ULA) | `fc00::/7` (commonly `fd00::/8`) | No | Private addressing within an organization |
| Global Unicast | `2000::/3` | Yes | Assigned by IANA to ISPs and organizations |
| Link-Local | `fe80::/10` | No | Communication between devices on the same link |
| Multicast | `ff00::/8` | Varies | One-to-many communication |
| Loopback | `::1` | No | Internal host communication (like 127.0.0.1) |

### IPv6 Address Representation -- Shortening Rules

Full Address: `2001:0000:0000:0001:0000:0000:0000:0001`

1. Remove leading zeros in each hextet: `2001:0:0:1:0:0:0:1`
2. Replace one sequence of consecutive zero hextets with `::`: `2001:0:0:1::1`

**Important Rules:**
- Leading zeros in each hextet can be omitted.
- Consecutive zero hextets can be replaced with `::` only once per address.
- `::` represents one or more groups of 0000, but must not be used more than once to avoid ambiguity.

---

## IPv6 Neighbor Discovery Protocol (NDP)

NDP is a core part of IPv6, replacing ARP and parts of DHCP. It enables devices to discover routers, resolve IP-to-MAC addresses, and maintain reachability information.

### Message Types

| Type | Code | Description |
|---|---|---|
| Router Solicitation | 133 | Sent by a host to request router information when joining a network. Multicast to `ff02::2`. |
| Router Advertisement | 134 | Sent by routers in response to solicitations or periodically. Contains prefix info, default gateway, MTU. |
| Neighbor Solicitation | 135 | Sent to determine the MAC address of a neighbor (similar to ARP). |
| Neighbor Advertisement | 136 | Sent in response to a Neighbor Solicitation. Contains the MAC address. |
| Redirect | 137 | Sent by a router to inform a host of a better next-hop for a destination. |

---

## Cisco Routing Table Codes -- Quick Reference

| Code | Protocol |
|---|---|
| L | Local route (interface IP address) |
| C | Connected route (directly attached network) |
| S | Static route (manually configured) |
| R | RIP (Routing Information Protocol) |
| M | Mobile (Mobile IP routing) |
| B | BGP (Border Gateway Protocol) |
| D | EIGRP (Enhanced Interior Gateway Routing Protocol) |
| EX | EIGRP external route |
| O | OSPF (Open Shortest Path First) |
| IA | OSPF inter-area route |
| N1 | OSPF NSSA external type 1 |
| N2 | OSPF NSSA external type 2 |
| E1 | OSPF external type 1 |
| E2 | OSPF external type 2 |
| i | IS-IS (Intermediate System to Intermediate System) |
| su | IS-IS summary route |
| L1 | IS-IS level-1 route |
| L2 | IS-IS level-2 route |
| ia | IS-IS inter-area route |
| * | Candidate default route |
| U | Per-user static route |
| o | ODR (On-Demand Routing) |
| P | Periodic downloaded static route |

---

## Administrative Distance (AD) -- Quick Reference

Administrative Distance is a value used by routers to rank the trustworthiness of different routing sources. Lower AD values are preferred over higher ones when multiple routes to the same destination exist.

| AD Value | Routing Source |
|---|---|
| 0 | Connected (directly attached interface) |
| 1 | Static route (manually configured) |
| 5 | EIGRP summary route |
| 20 | External BGP |
| 90 | EIGRP internal |
| 100 | IGRP |
| 110 | OSPF |
| 115 | IS-IS |
| 120 | RIP |
| 170 | Internal BGP |
| 200 | ODR |
| 255 | Unknown or unreachable (route will not be used) |

**Key Notes:**
- AD is only relevant when multiple routing protocols provide routes to the same destination.
- The route with the **lowest AD** is installed in the routing table.
- AD can be manually adjusted to influence route selection.

**Example:** If both RIP (AD 120) and OSPF (AD 110) provide a route to 192.168.1.0/24, the router will prefer the OSPF route.

---

## Routing vs. Routed -- Quick Reference

**Routing:**
- Refers to the process of selecting paths in a network along which to send data.
- Involves protocols that help routers exchange information and build routing tables.
- Focuses on **how** data gets from one point to another.
- Examples: RIP, OSPF, EIGRP, BGP

**Routed:**
- Refers to the actual data being transported across the network.
- Involves protocols that carry user traffic from source to destination.
- Focuses on **what** is being moved across the network.
- Examples: IP, IPv6, AppleTalk, IPX

> **Summary:** Routing is the decision-making process. Routed is the data-carrying mechanism. Routing protocols build the map; routed protocols use it to deliver the message.

---

## Autonomous Systems (AS)

An Autonomous System is a collection of connected IP routing prefixes under the control of one or more network operators, acting as a single administrative domain. It presents a unified routing policy to the Internet.

**Key Characteristics:**
- Identified by a unique 16-bit or 32-bit AS number (ASN)
- Used in BGP to define routing boundaries

**Examples:**

| ASN | Organization |
|---|---|
| AS109 | CISCO-EU-109 -- Cisco Systems Global ASN |
| AS193 | FORD-ASN -- Lockheed Martin Western Development Labs |
| AS721 | DoD Network Information Center Network |
| AS3598 | MICROSOFT-CORP-AS -- Microsoft Corporation |
| AS15169 | GOOGLE -- Google Inc. |

### Routing Protocol Concepts

**Distance Vector Protocols:**
- Share entire routing tables with neighbors at regular intervals
- Use hop count as the primary metric
- Examples: RIP, IGRP
- Pros: Simple to configure
- Cons: Slower convergence, risk of routing loops

**Link-State Protocols:**
- Share only topology changes (not full routing tables)
- Each router builds a complete map of the network
- Use Dijkstra's algorithm to calculate shortest paths
- Examples: OSPF, IS-IS

**LSA -- Link-State Advertisement:**
- A type of message used in link-state protocols (especially OSPF)
- Contains information about a router's interfaces, neighbors, and network links
- Flooded throughout the area to ensure all routers have synchronized topology data
- Enables fast convergence and accurate path selection

---

## First Hop Redundancy Protocols (FHRP)

### 1. HSRP -- Hot Standby Router Protocol
- Proprietary to Cisco
- Provides gateway redundancy by creating a virtual IP address shared between routers
- One router is active, others are in standby
- Only the active router forwards traffic

### 2. VRRP -- Virtual Router Redundancy Protocol
- Open standard (RFC 5798)
- Similar to HSRP but supports multiple vendors
- One router is elected as master; others are backups

### 3. GLBP -- Gateway Load Balancing Protocol
- Cisco proprietary
- Provides both redundancy and load balancing
- Multiple routers share the forwarding load
- One router acts as the Active Virtual Gateway (AVG); others are Active Virtual Forwarders (AVFs)

### Comparison Summary

| Protocol | Type | Vendor Support | Redundancy | Load Balancing |
|---|---|---|---|---|
| HSRP | Proprietary | Cisco only | Yes | No |
| VRRP | Open | Multi-vendor | Yes | No |
| GLBP | Proprietary | Cisco only | Yes | Yes |

---

## IPv4 Fragment Offset -- Reference Note

### What Is It?

The Fragment Offset field in the IPv4 header is a 13-bit value that indicates the position of a fragment's data relative to the beginning of the original, unfragmented IP packet.

**Range:** 13 bits = values from 0 to 8191. Each unit represents 8 bytes (not 1 byte).

### Formula to Calculate Fragment Offset

```
Offset = (MTU - (IHL x 4)) / 8
```

Where:
- MTU = Maximum Transmission Unit (typically 1500 bytes for Ethernet)
- IHL = Internet Header Length (usually 5 for standard IPv4 header)
- IHL x 4 = Header size in bytes (since IHL is in 32-bit words)

### Example Calculation

Assume MTU = 1500 bytes, IHL = 5, Header size = 20 bytes:

```
Offset = (1500 - 20) / 8
Offset = 1480 / 8
Offset = 185
```

The fragment offset field value is **185**, meaning this fragment starts **1480 bytes** into the original payload.

**Notes:**
- First fragment always has offset = 0
- Offset must divide evenly by 8
- Fragment offset is used to reassemble the original packet in order

**Use in Wireshark:**
- Look under the IPv4 header for "Fragment offset"
- Use `ip.id == 0xXXXX` to filter all fragments of the same packet
