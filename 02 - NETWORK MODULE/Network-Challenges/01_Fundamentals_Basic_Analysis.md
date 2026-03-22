# Networking Fundamentals - Task 3 - Basic Analysis

Berkeley Packet Filter (BPF) challenges using tcpdump.

> **Note:** All answers use BPF filter syntax with no spaces. Use `&&` and `||` operators only. CTFD will not accept TCPdump Primitives as answers. Keep your values in all the same format (HEX or Decimal) -- do not mix.

---

## Question 1 - TTL

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture all packets with a TTL of 64 and less, utilizing the IPv4 or IPv6 Headers? There should be 8508 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `ip[8]<=64||ip6[7]<=64`

---

## Question 2 - DF Bit Set

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture all IPv4 packets with at least the Dont Fragment bit set? There should be 2321 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `ip[6]&64==64`

---

## Question 3 - Source Port > 1024

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture traffic with a Source Port higher than 1024, utilizing the correct Transport Layer Headers? There should be 7805 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `tcp[0:2]>1024||udp[0:2]>1024`

---

## Question 4 - UDP Protocol

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture all Packets with UDP protocol being set, utilizing the IPv4 or IPv6 Headers? There should be 1277 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `ip[9]=17||ip6[6]=17`

---

## Question 5 - ACK/RST or ACK/FIN Flags

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture only packets with the ACK/RST or ACK/FIN flag set, utilizing the correct Transport Layer Header? There should be 1201 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `tcp[13]=20||tcp[13]=17`

---

## Question 6 - IP ID Field

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture all packets with an IP ID field of 213? There should be 10 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `ip[4:2]=213||ip[0]=0`

---

## Question 7 - VLAN Tag

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture all traffic that contains a VLAN tag? There should be 182 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `ether[12:2]=33024`

---

## Question 8 - DSCP Field

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture all IPv4 packets with the DSCP field of 37? There should be 42 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** (variation -- pending correct syntax for CTF)

Possible answers:
```bash
ip[1]=148||ip[0]=0
ip[1]=148||ip[1]=148
ip[1]&0xfc=148||ip[0]=0
(ip[1]&0xfc)==148||ip[0]=0
```

---

## Question 9 - Traceroute Detection

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture all IPv4 packets targeting just the beginning of potential traceroutes as it's entering your network. This can be from a Windows or Linux machine using their default settings. There should be 83 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `ip[8]=1&&(ip[9]=1||ip[9]=17)`

---

## Question 10 - DNS Packets

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture all DNS packets? There should be 63 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** [pending]

---

## Question 11 - TCP Connection Initiation (SYN)

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture the initial packets from a client trying to initiate a TCP connection? There should be 3447 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `tcp[13]=2`

---

## Question 12 - Open TCP Ports (SYN-ACK)

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture the response packets from a server listening on an open TCP ports? There should be 277 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `tcp[13]=18`

---

## Question 13 - Closed TCP Ports (RST)

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture the response packets from a server with closed TCP ports? There should be 17 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `tcp[13]=4`

---

## Question 14 - Well Known Ports

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture all TCP and UDP packets sent to the well known ports? There should be 3678 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** [pending]

---

## Question 15 - HTTP Traffic

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture all HTTP traffic? There should be 1404 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `tcp[0:2]=80||tcp[2:2]=80`

---

## Question 16 - Telnet Traffic

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture all telnet traffic? There should be 62 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `tcp[0:2]=23||tcp[2:2]=23`

---

## Question 17 - ARP Traffic

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture all ARP traffic in the most efficient manner? There should be 40 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `ether[12:2]=0x0806`

---

## Question 18 - VLAN Hopping

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture an attacker using vlan hopping to move from vlan 1 to vlan 10? There should be 15 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** [pending]

---

## Question 19 - Evil Bit

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture all packets that have the "Evil bit" set? There should be 197 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `ip[6]&0x80=0x80`

---

## Question 20 - URG Flag Not Set with URG Pointer Value

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture all packets where the URG flag is not set and URG pointer has a value? There should be 43 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `tcp[13]&0x20=0&&tcp[18:2]!=0`

---

## Question 21 - TCP Null Scan

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture a TCP null scan to the host 10.10.10.10? There should be 19 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `tcp[13]=0&&ip[16:4]=0x0a0a0a0a`

---

## Question 22 - CHAOS Protocol

**Prompt:**
What is the Berkeley Packet Filter, using tcpdump, to capture any packets containing the CHAOS protocol within an IPv4 header? There should be 139 packets.

**Instructions:**
- Enter the Filter syntax with no spaces.
- CTFD will only accept BPF filters, `&&`, and `||` as answers with no spaces.
- CTFD will not accept TCPdump Primitives as answers.
- Keep your values in all the same format. HEX or Decimal. Do not mix.

**Answer:** `ip[9]=16`
