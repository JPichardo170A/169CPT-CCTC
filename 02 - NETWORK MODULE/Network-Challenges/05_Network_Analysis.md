# Network Analysis Challenges

## Question 1 -- Attack Analysis - Total Packets

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> How many total packets were captured in the pcap?
> Enter number with no comma's

### Steps Taken

1. Opened the PCAP file in Wireshark
2. Checked the packet count at the bottom status bar

**Answer:** `1908895`

### Notes

- Large volume of packets suggests extended capture duration or high traffic volume
- Useful for estimating analysis scope and filtering strategy

---

## Question 2 -- Attack Analysis - IPs

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> Determine all IP addresses that were captured in the PCAP, and list them in order.
> You should find 10.
> Example: 1.1.1.1,2.2.2.2,3.3.3.3

### Steps Taken

1. Opened the PCAP file in Wireshark
2. Navigated to Statistics > Endpoints > IPv4
3. Collected all unique IP addresses
4. Corrected invalid entries (299 -> 199)
5. Sorted them numerically, octet by octet
6. Verified total count = 10

**Answer:** `91.189.89.199,192.168.10.101,192.168.10.111,192.168.10.112,192.168.41.1,192.168.41.2,192.168.41.130,192.168.41.254,224.0.0.251,239.255.255.250`

---

## Question 3 -- Attack Analysis - Hosts

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> How many hosts are in the capture?
> Hint: What types of IP address can be assigned to a host?

### Steps Taken

1. Collected all IPs from the capture:
   `91.189.89.199, 192.168.10.101, 192.168.10.111, 192.168.10.112, 192.168.41.1, 192.168.41.2, 192.168.41.130, 192.168.41.254, 224.0.0.251, 239.255.255.250`
2. Identified which IPs are assigned to hosts (unicast IPs):
   - Public IP: 91.189.89.199
   - Private IPs: 192.168.10.101, 192.168.10.111, 192.168.10.112, 192.168.41.1, 192.168.41.2, 192.168.41.130, 192.168.41.254
3. Excluded multicast addresses (224.0.0.251, 239.255.255.250) from host count

**Answer:** `8`

### Notes

- Only unicast IPs (public/private) are considered "host-assigned"
- Multicast IPs are used for group communication and are not counted as hosts

---

## Question 4 -- Attack Analysis - OSI Layer

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> What Transport Layer Protocol is the most prominent in the capture?

### Steps Taken

1. Opened the PCAP file in Wireshark
2. Navigated to Statistics > Protocol Hierarchy
3. Checked the Transport Layer protocols (TCP, UDP, etc.) and their relative packet counts
4. Observed that UDP had the highest number of packets compared to TCP and others

**Answer:** `UDP`

### Notes

- UDP is a connectionless protocol commonly used for DNS, mDNS, SSDP, and other discovery services
- TCP packets were present but fewer than UDP
- The capture's traffic shows UDP is the dominant Transport Layer protocol

---

## Question 5 -- Attack Analysis - OS Type

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 10 |

**Prompt:**
> Use p0f to read the PCAP and determine the OS type of the host: 192.168.10.101

### Steps Taken

1. Filtered PCAP for the target host:

```bash
tcpdump -r attack_analysis1.pcap host 192.168.10.101 -w filtered_192.168.10.101.pcap
```

2. Ran p0f on the filtered PCAP:

```bash
sudo p0f -r filtered_192.168.10.101.pcap
```

3. Observed p0f output for the host 192.168.10.101:
   - Reports TCP SYN/SYN+ACK, MTU, uptime, HTTP request/response fingerprints
4. Interpreted fingerprint patterns to determine OS type

**Answer:** `Linux`

### Notes

- p0f passively identifies OS based on TCP/IP stack characteristics
- Observed TCP/HTTP/uplink patterns match Linux behavior
- This is a passive analysis; no traffic was generated

---

## Question 6 -- Attack Analysis - Cellular

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> There is traffic related to 2G & 3G Cellular Communications, which uses a packet-oriented mobile data standard.
> What standard/protocol is performing this communication?
> Be sure to name the protocol and not the standard being used!

### Steps Taken

1. Opened the PCAP in Wireshark
2. Applied filters and examined cellular packet traffic
3. Identified packet-oriented sessions carrying user data
4. Determined that the communication protocol is GPRS

**Answer:** `GPRS`

### Notes

- GPRS (General Packet Radio Service) is the protocol used for packet-oriented mobile data over 2G/3G networks
- It is responsible for transferring user data between mobile devices and the core network
- The underlying standards (GSM, UMTS) define the network, but GPRS is the actual data protocol in use

---

## Question 7 -- Attack Analysis - Address 1 - Types

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 5 |

**Prompt:**
> Within the packet capture, the following IP Address was identified:
> 239.x.x.x
> What type of address is this?

### Steps Taken

1. Identified the IP address in the capture: 239.255.255.250
2. Checked the IP address range:
   - 224.0.0.0 to 239.255.255.255 is the IPv4 **multicast range**
   - Specifically, 239.x.x.x addresses fall under **administratively scoped multicast**
3. Determined its role:
   - Used for group communication in local/private networks
   - Commonly seen in SSDP (Simple Service Discovery Protocol) traffic

**Answer:** `Multicast`

### Notes

- 239.x.x.x addresses are not assigned to individual hosts
- They are used to send packets to multiple devices subscribed to the group
- This address was observed in the capture as part of service discovery traffic

---

## Question 8 -- Attack Analysis - Address 2 - Protocol

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> The protocol being used to generate the traffic associated with 239.x.x.x is a UDP based protocol which is commonly used with UPnP for finding devices and services on the network.
> What is this protocol?

### Steps Taken

1. Opened the PCAP file in Wireshark
2. Applied a display filter to isolate traffic to the multicast address 239.255.255.250:

```
udp.dstport == 1900
```

3. Observed that this traffic uses UDP and is consistent with service discovery messages
4. Checked protocol column in Wireshark; the traffic is identified as SSDP (Simple Service Discovery Protocol)
5. Verified that this protocol is commonly used by UPnP to find devices and services on the network

**Answer:** `SSDP`

### Notes

- SSDP is a UDP-based protocol using port 1900 for multicast discovery
- It communicates with 239.255.255.250 to locate UPnP devices/services
- Observed traffic confirms that this multicast address is being used for device discovery in the capture

---

## Question 9 -- Attack Analysis - Address 3 - Source

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> What is the MAC address of the device that is sending the multicast SSDP traffic?
> Example: 00:00:00:00:00:00

### Steps Taken

1. Opened the PCAP file in Wireshark
2. Applied a display filter to isolate the SSDP traffic:

```
udp.dstport == 1900
```

3. Looked at the Source column in the packet list to identify the sending IP
4. Opened one SSDP packet and expanded the Ethernet II header
5. Noted the Source MAC address: `00:50:56:c0:00:08`
6. Verified that all multicast SSDP packets use the same source MAC address

**Answer:** `00:50:56:c0:00:08`

### Notes

- This MAC address identifies the physical device generating the SSDP traffic
- Multicast traffic has destination MACs in the range 01:00:5E:xx:xx:xx
- Only the source MAC identifies the originating host

---

## Question 10 -- Attack Analysis - Address 4 - User Agent

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> What user agent is making use of the protocol you discovered in Attack Analysis - Address 2 - Protocol?

### Steps Taken

1. Opened the PCAP file in Wireshark
2. Applied a display filter for SSDP traffic:

```
udp.dstport == 1900 || ip.dst == 239.255.255.250
```

3. Selected an SSDP packet and expanded the Simple Service Discovery Protocol (SSDP) section
4. Looked at the HTTP headers within the SSDP payload
5. Found the User-Agent field indicating which application is sending the request
6. Verified consistency across multiple SSDP packets in the capture

**Answer:** `Google Chrome/58.0.3029.110 Windows`

### Notes

- The User-Agent shows the application and platform initiating SSDP requests
- SSDP is commonly used by browsers and media software to discover devices/services on the network
- This confirms that the SSDP traffic is originating from Chrome on Windows in this capture

---

## Question 11 -- Attack Analysis - DNS 1 - IP

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> What is the IP Address for the DNS Server in the packet capture?

### Steps Taken

1. Opened the PCAP file in Wireshark
2. Applied a display filter for DNS traffic:

```
dns
```

3. Observed DNS queries and responses in the packet list
4. Noted the Destination IP for DNS queries from hosts in the capture
5. Verified which IP consistently responds to queries (the authoritative DNS server)

**Answer:** `192.168.41.2`

### Notes

- This IP address corresponds to the DNS server responding to requests in the capture
- DNS traffic uses UDP port 53; both queries and responses were used to identify the server
- Hosts in the 192.168.x.x network are configured to use this server for name resolution

---

## Question 12 -- Attack Analysis - DNS 2 - Port

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> What IP Address and Port is the query responding to?
> Example: XXX.XXX.XXX.XXX:PORT

### Steps Taken

1. Opened the PCAP file in Wireshark
2. Applied a display filter for DNS traffic:

```
dns
```

3. Selected a DNS response packet in the packet list
4. Expanded the UDP or DNS header to see source and destination information
5. Noted the Destination IP and Port where the DNS response was sent
6. Verified consistency across multiple responses from the DNS server

**Answer:** `192.168.10.111:54966`

### Notes

- DNS responses use the same port that the client used for the query (source port on client side)
- In this capture, the host 192.168.10.111 received responses from the DNS server at port 54966
- This confirms correct client-server communication for DNS

---

## Question 13 -- Attack Analysis - Service 1

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> What is the Service indicated by the use of the following IP Address?
> 224.0.0.251
> Hint: Google search the Multicast IP

### Steps Taken

1. Noted the IP address from the packet capture: 224.0.0.251
2. Recognized that 224.0.0.0/24 is a multicast address range
3. Googled "224.0.0.251 multicast" to identify its service
4. Found that 224.0.0.251 is reserved for mDNS (Multicast DNS)
5. Verified in Wireshark that the traffic to 224.0.0.251 uses UDP port 5353, confirming mDNS usage

**Answer:** `mDNS`

### Notes

- mDNS is used for local network service discovery without a central DNS server
- This multicast IP allows hosts to query for local services in the 224.0.0.251 address space
- Observed traffic in the capture confirms its use for device discovery on the network

---

## Question 14 -- Attack Analysis - Service 2

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> What is the FQDN and IP Address of the device indicated in the response to the query identified in Attack Analysis - Service 1? Look for the DNS A record.
> Example (No Spaces): keurig.domain,1.1.1.1

### Steps Taken

1. Opened the PCAP file in Wireshark
2. Applied a display filter to isolate mDNS traffic:

```
udp.port == 5353 && ip.dst == 224.0.0.251
```

3. Selected a response packet to the mDNS query
4. Expanded the DNS section in the packet details
5. Located the A record in the response to find the device's IP
6. Noted the FQDN and IP address from the A record
7. Verified consistency across multiple response packets

**Answer:** `HP705A0FF92F8D.local,192.168.1.7`

### Notes

- The FQDN is the hostname of the device providing the service via mDNS
- The IP address is assigned within the local network
- This confirms which device is responding to mDNS queries on 224.0.0.251

---

## Question 15 -- Attack Analysis - Vulnerability

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> Attackers will seek unique ways to avoid being caught. This traffic has been reported to contain a vulnerability that crashes Wireshark due to an out-of-bounds write, detailed in CVE-2017-13766.
> What Protocol did the attackers use to achieve this and which server IP Address and Port was targeted?
> Example: PROTOCOL 1.1.1.1:PORT

### Steps Taken

1. Reviewed CVE-2017-13766, which affects the Profinet I/O dissector in Wireshark
2. Opened the PCAP file in Wireshark
3. Applied a display filter for the target IP:

```
ip.addr == 192.168.10.111
```

4. Examined packets sent to this IP to identify the destination port
5. Observed the Profinet I/O packets targeting port 55
6. Verified that all malicious Profinet packets in the capture consistently targeted this server and port

**Answer:** `PROFINET 192.168.10.111:55`

### Notes

- Using the filter `ip.addr == 192.168.10.111` made it easy to isolate traffic to the target server
- The vulnerability (CVE-2017-13766) allows specially crafted Profinet packets to trigger an out-of-bounds write in Wireshark
- This confirms the attackers used Profinet I/O protocol to target IP 192.168.10.111 on port 55

---

## Question 16 -- Attack Analysis - IOT

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> It was identified that an exploit targeting a prominent IOT System was captured targeting 192.168.10.111 over UDP port 55.
> This protocol was identified as an open global standard for wireless technology that uses low-power digital radio signals for indoor Personal Area Networks, uses the IEEE 802.15.4 specification as its basis, which is often deployed in a mesh topology.
> What is the name of this Protocol and what is the Packet Type being flooded?
> Example: (NoSpaces) PROTOCOL,PACKET_TYPE

### Steps Taken

1. Filtered traffic in Wireshark for `ip.addr == 192.168.10.111 && udp.port == 55`
2. Opened Statistics > Protocol Hierarchy to identify encapsulated protocols
3. Located Zigbee under UDP and applied filter to isolate packets
4. Examined Data field / Payload of packets in middle pane
5. Observed repeated "hello" packets being sent to the target

**Answer:** `Zigbee,hello`

### Notes

- Protocol is Zigbee (IEEE 802.15.4 over UDP)
- Packet Type flooded is "hello"
- Observed repeated packets targeting UDP port 55 confirming flood

---

## Question 17 -- Attack Analysis - RCE

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> Remote arbitrary Code Execution was captured targeting 192.168.10.111 via a gaming protocol.
> What is the name of the game?
> Example: fortnite

### Steps Taken

1. Filtered traffic in Wireshark for `ip.addr == 192.168.10.111`
2. Opened Statistics > Protocol Hierarchy
3. Expanded UDP to locate encapsulated protocols
4. Found Quake III listed under UDP, confirming the target game

**Answer:** `Quake III`

### Notes

- Protocol associated with RCE traffic is Quake III
- Traffic was observed targeting UDP ports specific to the game

---

## Question 18 -- Attack Analysis - Vuze

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> The Vuze DHT protocol was used as an exploit against 192.168.10.111, indicated in the protocol hierarchy page of Wireshark.
> After analysis and some Open Source Research, what type of Application is Vuze?

### Steps Taken

1. Filtered traffic in Wireshark for `ip.addr == 192.168.10.111`
2. Opened Statistics > Protocol Hierarchy
3. Located Vuze DHT under the protocol list
4. Conducted online research to identify Vuze as a BitTorrent client application

**Answer:** `BitTorrent Client`

### Notes

- Vuze DHT traffic indicates peer-to-peer file-sharing activity
- Exploit leverages the application protocol to target the host

---

## Question 19 -- Attack Analysis - Conversation

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> Determine the IP addresses for the top two talkers in the capture (the two hosts that generated the most traffic).
> List in order e.g. 1.1.1.1,2.2.2.2

### Steps Taken

1. Opened the PCAP file in Wireshark
2. Navigated to Statistics > Conversations
3. Selected the IPv4 tab to view all IP conversations
4. Sorted by Packets or Bytes in descending order
5. Identified the top two IPs generating the most traffic

**Answer:** `192.168.10.101,192.168.10.111`

### Notes

- 192.168.10.101 and 192.168.10.111 are the most active hosts in the capture
- Sorting by bytes or packets ensures accurate identification of top talkers

---

## Question 20 -- Attack Analysis - OS Fingerprint

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> Initial TTL can be used to determine host operating systems.
> Use a tool that will perform fingerprinting based on other criteria to determine the OS of the host with the IP address 192.168.10.111.

### Steps Taken

1. Filtered the PCAP to isolate traffic for 192.168.10.111:

```bash
tcpdump -r attack_analysis1.pcap host 192.168.10.111 -w filtered_192.168.10.111.pcap
```

2. Ran p0f on the filtered PCAP to fingerprint the OS:

```bash
sudo p0f -r filtered_192.168.10.111.pcap | grep "192.168.10.111"
```

3. Examined p0f output to identify the operating system based on TCP/IP characteristics

**Answer:** `Linux`

### Notes

- p0f uses TCP/IP stack fingerprinting, including TTL, window size, and options
- Filtering the PCAP first helps focus only on the target host
- Confirms that 192.168.10.111 is running a Linux-based OS

---

## Question 21 -- Attack Analysis - Attacked Port

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> Filter traffic communication between the IP addresses of the hosts determined in challenge 19.
> A UDP flood is occurring. What port is being attacked?

### Steps Taken

1. Filtered the capture in Wireshark using:

```
ip.addr == 192.168.10.101 && ip.addr == 192.168.10.111
```

2. Applied UDP filter to narrow down to UDP traffic
3. Observed repeated packets targeting the same destination port
4. Checked packet details in the middle pane to confirm the port under UDP header > Destination Port

**Answer:** `55`

### Notes

- UDP flood is confirmed between 192.168.10.101 (source) and 192.168.10.111 (target)
- Destination port 55 is being repeatedly targeted
- Supports findings from previous questions regarding IoT/Zigbee flood

---

## Question 22 -- Attack Analysis - Attacked Port 2

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> In the last challenge you discovered port 55 being targeted for attacks.
> This is within the well-known port range. What typical service is associated with it?

### Steps Taken

1. Verified the previously identified attacked port (UDP port 55) from Question 21
2. Cross-referenced the port with the IANA well-known port assignments
3. Found that port 55 corresponds to the service ISI-GL
4. Confirmed in Wireshark under UDP header > Destination Port that the service aligns with known port usage

**Answer:** `ISI-GL`

### Notes

- Port 55 is a well-known UDP port associated with ISI-GL
- This reinforces the identification of the targeted service in the UDP flood attack

---

## Question 23 -- Attack Analysis - Type of Attack

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> What type of attack is the UDP flood discovered in challenge 22?

### Steps Taken

1. Filtered traffic in Wireshark for the two top talkers identified in Challenge 19:

```
ip.addr == 192.168.10.101 && ip.addr == 192.168.10.111
```

2. Applied a UDP filter to isolate UDP traffic
3. Observed repeated packets targeting port 55
4. Noted the high volume and repetitive nature of packets, indicating a flood attack
5. Identified the attack as a Denial of Service (DoS) based on traffic behavior and impact potential

**Answer:** `DOS`

### Notes

- UDP flood attacks overwhelm the target with traffic, disrupting normal service
- The attack matches DoS characteristics: high packet rate, same destination port, same target host
- Confirms findings from Challenge 22 (attacked port 55 / ISI-GL service)

---

## Question 24 -- Attack Analysis - Type of Attack 2

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> Is this an automated attack? (yes/no)
> Once you have completed challenge questions 1 - 24 you can shorten the PCAP to make Wireshark run faster.

### Steps Taken

1. Opened the original PCAP in Wireshark
2. Applied a filter to exclude the UDP port 55 flood traffic:

```
!(udp.port==55)
```

3. Exported the selected packets using File > Export Specified Packets
4. Saved the new PCAP as **attack_analysis2.pcap**, reducing the packet count from ~1.9 million to 86,345 packets
5. Reviewed traffic patterns in the new PCAP and noted repetitive, high-volume UDP flooding
6. Concluded the attack is automated due to its high repetition and uniform targeting

**Answer:** `yes`

### Notes

- Shortening the PCAP makes analysis faster in Wireshark
- Automated attacks typically exhibit high-frequency, uniform traffic patterns
- The filtered PCAP confirms the automated nature of the attack

---

## Question 25 -- Attack Analysis - Server Version

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> What version of Apache is running on the web server at the 192.168.10.111 address according to raw signatures in p0f?

### Steps Taken

1. Filtered traffic in Wireshark for HTTP communication to the server:

```
ip.addr == 192.168.10.111 && tcp.port == 80
```

2. Exported the filtered packets to a new PCAP if necessary
3. Ran p0f on the filtered PCAP to perform passive OS and service fingerprinting:

```bash
sudo p0f -r filtered_192.168.10.111.pcap | grep "192.168.10.111"
```

4. Observed server responses in SYN/SYN+ACK, HTTP, and MTU probes
5. Noted the Apache version reported in p0f raw signatures under `server = 192.168.10.111/80`

**Answer:** `Apache 2.4.18`

### Notes

- p0f uses TCP/IP fingerprinting and HTTP response analysis to identify server version
- This confirms the web server is running Apache 2.4.18 on port 80

---

## Question 26 -- Attack Analysis - Website Tool

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> An attacker performed a plugin scan against the 192.168.10.111 server.
> There are several HTTP POST/GET messages related to this scan.
> What is the name of the website-creation software/tool used on the 192.168.10.111 server?
> (Supply the main software name, not the plugin names.)

### Steps Taken

1. Opened the PCAP in Wireshark
2. Applied a filter to capture HTTP GET/POST requests to the server:

```
http.request.method=="GET" || http.request.method=="POST"
ip.dst == 192.168.10.111
```

3. Inspected the HTTP request URIs and headers in the packet details pane
4. Identified the main software name from the request path: `/wordpress/`
5. Ignored plugin names in the query parameters

**Answer:** `WordPress`

### Notes

- HTTP GET/POST requests can reveal the CMS or web framework
- Focus on main platform identifiers in the URL path or HTTP headers
- This confirms the target server uses WordPress

---

## Question 27 -- Attack Analysis - Scanning Tools

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> WordPress provides a plethora of plugins, however these are notorious for vulnerabilities, and there are several ways to scan these types of servers. Perform OSR on some of the top tools that could be used.
> Determine which 2 tools were used for scanning against 192.168.10.111.
> These tools can be identified through examining the user-agent strings.
> (The answer has no spaces)

### Steps Taken

1. Opened the PCAP in Wireshark
2. Filtered for HTTP traffic to the server:

```
ip.dst == 192.168.10.111 && http.request
```

3. Inspected the HTTP request headers, focusing on `User-Agent:` strings
4. Noted any known scanning tools appearing in the User-Agent (e.g., Nikto, DirBuster, WPScan, etc.)
5. Cross-referenced User-Agent strings with OSR/research to confirm legitimate scanning tools
6. Selected the top 2 tools that actively scanned the WordPress server

**Answer:** `Nikto,WPScan`

### Notes

- User-Agent strings are key indicators of automated scanning tools
- Focus only on tools actually performing HTTP requests in the capture
- Ensure the answer is written without spaces and in order of identification if required

---

## Question 28 -- Attack Analysis - Credentials

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 10 |

**Prompt:**
> What is the username and password that was attempted against the axis2 plugin?
> Submit answer in the following format: jeff:mynamisjeff

### Steps Taken

1. Opened the PCAP in Wireshark
2. Filtered for HTTP POST requests to the axis2-admin login:

```
http.request.method == "POST" && ip.dst == 192.168.10.111 && http.request.uri contains "axis2-admin/login"
```

3. Selected a POST request and expanded the Form URL Encoded Data section in the packet details pane
4. Observed the form fields: `userName` and `password`
5. Noted the credentials attempted:
   - userName: **admin**
   - password: **axis2**

**Answer:** `admin:axis2`

### Notes

- The credentials are visible in cleartext in the POST request body
- Always check for `application/x-www-form-urlencoded` content type when analyzing login attempts
- Filtering by both HTTP method and target URI helps narrow down the packets efficiently

---

## Question 29 -- Attack Analysis - Plugin

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 5 |

**Prompt:**
> Consider the user agent strings identified in challenge 27.
> Analyze the related traffic, and identify what Plugin the vulnerability scan triggered on?

### Steps Taken

1. Opened the PCAP in Wireshark
2. Filtered by the scanner User-Agent strings from challenge 27 (e.g., Nikto)
3. Followed the HTTP traffic and inspected the Request URI paths
4. Located requests under `/wp-content/plugins/`
5. Determined the plugin being targeted was `reflex-gallery`

**Answer:** `reflex-gallery`

### Notes

- The scanner was probing the plugin for known vulnerabilities
- The plugin name is extracted from the path immediately following `/plugins/`

---

## Question 30 -- Attack Analysis - Plugin CVE

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> Refer to challenge 29. What CVE is related to the plugin the web vulnerability scanner found?
> (You need to include the version in your research)

### Steps Taken

1. Identified the plugin from challenge 29 as `reflex-gallery`
2. Conducted open source research (OSR) for known vulnerabilities associated with `reflex-gallery`
3. Confirmed the vulnerability affecting older versions of the plugin is listed as CVE-2015-4133
4. Verified CVE details to ensure it aligns with the scanning activity in the PCAP

**Answer:** `CVE-2015-4133`

### Notes

- The CVE affects `reflex-gallery` WordPress plugin versions prior to the patched release
- Vulnerability allows unauthorized access/execution, as confirmed by Nikto scanner traffic

---

## Question 31 -- Attack Analysis - Exploit

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> Reading the CVE details will provide you with the knowledge to know what the attacker is able to exploit.
> What was the Filename that was successfully uploaded by the attacker to 192.168.10.111?

### Steps Taken

1. Referenced the CVE-2015-4133 details to understand the exploit mechanism
2. Filtered traffic in Wireshark for HTTP POST requests to the vulnerable `reflex-gallery` plugin
3. Followed the TCP stream of the relevant POST request
4. Inspected the header section and identified the uploaded file: `filename="msf.php"`

**Answer:** `msf.php`

### Notes

- The file `msf.php` corresponds to the Metasploit payload commonly used in exploit demonstrations
- Confirms successful exploitation of the Reflex Gallery vulnerability on the target host

---

## Question 32 -- Attack Analysis - Exploit 2

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> The malicious upload referred to in challenge 31 is used to start communication for a specific tool, what is the name of this tool/framework (not the attack payload)?

### Steps Taken

1. Reviewed the uploaded file `msf.php` from challenge 31
2. Checked open source references (Exploit-DB) for Reflex Gallery CVE-2015-4133
3. Identified that `msf.php` is a payload associated with the Metasploit framework
4. Verified that the tool initiating the attack communication is Metasploit

**Answer:** `metasploit`

### Notes

- This confirms that the exploit used a standard penetration testing framework
- Metasploit is commonly used for remote code execution testing and proof-of-concept exploits

---

## Question 33 -- Attack Analysis - Payload

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> Refer to challenge 32. Perform open-source research:
> This popular attack payload provides an interactive shell to the attacker, this payload uses in-memory DLL injection. Identify the payload name (single word).

### Steps Taken

1. Reviewed challenge 32: the attack used Metasploit framework and `msf.php` upload
2. Performed open-source research on common Metasploit payloads
3. Identified payloads that provide interactive shells and use in-memory DLL injection
4. Confirmed that the payload matching this description is Meterpreter

**Answer:** `Meterpreter`

### Notes

- Meterpreter provides a stealthy interactive shell to attackers
- It operates mostly in memory, making it harder to detect
- Frequently used with Metasploit exploits for post-exploitation

---

## Question 34 -- Attack Analysis - Language

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> What programming language was the answer to question 32 (Metasploit) written in?

### Steps Taken

1. Reviewed the Metasploit framework documentation
2. Confirmed that the core Metasploit framework and modules are written in Ruby
3. Verified through official sources and open-source repositories

**Answer:** `Ruby`

### Notes

- Metasploit is primarily written in Ruby, which allows easy scripting and module creation
- Meterpreter payloads can be generated for multiple platforms, but the framework itself is Ruby-based

---

## Question 35 -- Attack Analysis - Payload UUID

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> Referring to the payload identified in Challenge 33 (Meterpreter), what is the Payload UUID identified in the session of the host that was first compromised?

### Steps Taken

1. Followed the TCP stream associated with the initial Meterpreter session
2. Inspected the payload handshake and session data for the UUID
3. Verified the session belongs to the first compromised host

**Answer:** `\x81\xc2\xe4\x1d\xc3\x06\xf6\xf6\xeb\xd8\xf8\xd7\xb2\xe2\xea\x5b`

### Notes

- Payload UUID is used to uniquely identify Meterpreter sessions
- This UUID corresponds to the first host that was compromised

---

## Question 36 -- Attack Analysis - Attacked IP

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> The 192.168.10.111 web server is now under control of the attacker through a reverse TCP connection via the Meterpreter session.
> The 192.168.10.111 server is used to pivot to another target and perform the same actions the attacker took against 192.168.10.111.
> What is its IP address?

### Steps Taken

1. Filtered the capture to follow the Meterpreter/reverse TCP session from the initially compromised host:
   - Example filter: `ip.addr == 192.168.10.111 && tcp`
2. Followed the relevant TCP stream(s) (right-click > Follow > TCP Stream) to observe session activity and commands
3. Looked for evidence of outbound connections originating from 192.168.10.111 to other internal IPs (new reverse shells, SMB, RDP, or custom backdoor connections)
4. Noted repeated connection attempts and successful sessions from 192.168.10.111 to 192.168.10.112 (same patterns of exploit/post-exploit actions as seen on .111)
5. Verified the pivot by checking that the same Meterpreter handshake, payload activity, or command execution appears between the attacker-controlled host and 192.168.10.112

**Answer:** `192.168.10.112`

### Notes

- Pivoting evidence includes new reverse TCP connections from the compromised host to the secondary target and replayed post-exploitation traffic patterns
- Use `Follow TCP Stream` and filters like `ip.addr == 192.168.10.111 || ip.addr == 192.168.10.112` to validate lateral movement

---

## Question 37 -- Attack Analysis - Malware Type

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 10 |

**Prompt:**
> What type of malware is uploaded to the 192.168.10.112 server (not the malicious PHP upload to kick off the Meterpreter session)?
> Look for a connection back to a malware repository in the TCP stream.

### Steps Taken

1. Opened the PCAP file in Wireshark
2. Applied display filter to locate the PHP upload:

```
ip.addr == 192.168.10.112 && http.request.method == "POST" && http.request.uri contains ".php"
```

3. Identified the POST request to:
   - `/wordpress/wp-content/plugins/reflex-gallery/admin/scripts/FileUploader/php.php`
   - Uploaded file: `msf2.php`
4. Followed the TCP stream of the POST request to inspect payload behavior
5. Observed reverse shell connection initiated to `192.168.10.101:444`
6. Applied filter to inspect traffic between `.112` and `.101` on port 444:

```
ip.addr == 192.168.10.112 && ip.addr == 192.168.10.101 && tcp.port == 444
```

7. Followed the TCP stream and discovered embedded references to GitHub:
   - `github.com/mauri870/ransomware`
8. Verified that this GitHub repository contains a Go-based ransomware framework
9. Observed function names and modules in the stream:
   - `cmd.PrintBanner`, `cmd.CheckOS`, `vendor/github.com/mattn/go-colorable`
10. Based on the repository and stream contents, determined the malware type is ransomware

**Answer:** `Ransomware`

---

## Question 38 -- Attack Analysis - New Payload UUID

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> What is the payload UUID for the new Meterpreter session on the host at 192.168.10.112?
> Enter answer in this format: \x11\x22\x33\x44\x55\x66\x77\x88\x99\xaa\xbb\xcc\xdd\xee\xff

### Steps Taken

1. Opened the PCAP file in Wireshark
2. Applied display filter to isolate traffic between the attacker and target:

```
ip.addr == 192.168.10.112 && ip.addr == 192.168.10.101 && tcp.port == 444
```

3. Located the relevant TCP stream for the Meterpreter session:
   - Frame 85476
   - Stream index: 515
4. Followed the TCP stream to inspect payload data
5. Searched the TCP payload for a 16-byte UUID in hexadecimal format
6. Successfully extracted the UUID from the payload

**Answer:** `\xc5\x0f\xbc\x3a\x9f\x31\x91\x0b\x42\x66\x51\x69\x1b\x5c\x43\xa3`

---

## Question 39 -- Attack Analysis - Malware Location

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> Refer back to challenge 37, the malware referenced in this question was identified as ransomware.
> What is the GitHub repo from which the malware is downloaded?
> (submit your answer in the following format: https://github.com/horst_simco/malwarez)

### Steps Taken

1. Revisited challenge 37, which confirmed the malware downloaded to 192.168.10.112 was ransomware
2. Followed the TCP stream of the reverse shell connection initiated by msf2.php
3. Inspected the payload data exchanged between 192.168.10.101 and 192.168.10.112 on port 444
4. Located embedded references to a GitHub repository within the TCP payload
5. Identified the full repository path: `github.com/mauri870/ransomware`
6. Verified that this repository contains a Go-based ransomware framework, matching the observed behavior and function names in the stream

**Answer:** `https://github.com/mauri870/ransomware`

---

## Question 40 -- Attack Analysis - Malware Language

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> According to the GitHub site, the malware was determined to be ransomware.
> What programming language was used to write this?

### Steps Taken

1. Revisited challenge 39, which identified the malware repository as:
   - `https://github.com/mauri870/ransomware`
2. Inspected the GitHub repository contents directly
3. Observed the presence of Go-specific files:
   - `go.mod`, `go.sum`, and multiple `.go` source files
4. Verified that the ransomware was written in the Go programming language, also known as Golang
5. Confirmed via the repository README and file structure that Go was used for both the core logic and supporting modules

**Answer:** `Go`

---

## Question 41 -- Attack Analysis - OS Target

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> Refer back to challenge 38, the malware referenced in this question was identified as ransomware.
> What OS is targeted by the malware?

### Steps Taken

1. Revisited challenge 38, which extracted the payload UUID from the Meterpreter session on 192.168.10.112
2. Reconfirmed from challenge 37 and 39 that the malware was downloaded from:
   - `https://github.com/mauri870/ransomware`
3. Inspected the GitHub repository and observed that the ransomware is written in Go
4. Reviewed the source code and README, which reference Windows-specific APIs and behaviors
5. Observed functions and modules tailored for Windows environments, such as file system access and OS detection logic
6. Verified that the ransomware targets Windows OS systems

**Answer:** `Windows`

---

## Question 42 -- Attack Analysis - Architecture

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> The ransomware indicated in challenge 37 targets what type of system architecture?

### Steps Taken

1. Revisited challenge 37, which identified the ransomware downloaded from:
   - `https://github.com/mauri870/ransomware`
2. Inspected the GitHub repository and reviewed the README and source files
3. Observed that the project includes a `ransomware.manifest` file and Windows-specific build instructions
4. Noted that the compiled binary is named `ransomware.exe`, indicating a Windows PE format
5. Verified that the malware is designed to run on 32-bit Windows systems, consistent with the Win32 classification
6. Confirmed via Microsoft Defender detection name: `Ransom:Win32/MauriCrypt.MK!MTB`, which indicates a 32-bit architecture target

**Answer:** `x86`

---

## Question 43 -- Attack Analysis - Assembly Description

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> What is the assembly description attribute in the assembly manifest for the ransomware?

### Steps Taken

1. Revisited challenge 39, which identified the ransomware source as:
   - `https://github.com/mauri870/ransomware`
2. Navigated to the `ransomware.manifest` file within the repository
3. Inspected the XML structure of the manifest to locate the `<description>` tag
4. Found the assembly description attribute embedded in the manifest metadata
5. Verified the exact value of the description field

**Answer:** `Nothing to see here`

---

## Question 44 -- Attack Analysis - Date

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> In the PCAP, there is a protocol that provides clock synchronization between systems over packet-switched networks in this PCAP.
> Use the information within this protocol's header to determine the date of this packet capture.
> (format your answer as follows: Oct 1, 2019)

### Steps Taken

1. Opened the PCAP file in Wireshark
2. Searched for a protocol used for clock synchronization
3. Identified the Network Time Protocol (NTP) as the relevant protocol
4. Applied display filter to isolate NTP traffic:

```
ntp
```

5. Located Frame 7, which contains an NTP request from 192.168.41.130 to ntp.ubuntu.com (91.189.89.199)
6. Inspected the NTP header and extracted the Transmit Timestamp field
7. The timestamp value was:
   - `Jun 9, 2017 01:59:27.092172825 UTC`
8. Used this timestamp to determine the date of the packet capture

**Answer:** `Jun 9, 2017`
