# Networking Fundamentals - Basic Analysis Challenges

Berkeley Packet Filter (BPF) challenges using tcpdump.

> **Note:** All answers use BPF filter syntax with no spaces. Use `&&` and `||` operators. Do not mix HEX and Decimal formats.

---

## Question 1

**Prompt:** Capture all packets with a TTL less than or equal to 64. (8508 packets)

**Answer:** `ip[8]<=64||ip6[7]<=64`

---

## Question 2

**Prompt:** Capture all packets with the Don't Fragment (DF) bit set. (2321 packets)

**Answer:** `ip[6]&64==64`

---

## Question 3

**Prompt:** Capture all packets with a Source Port greater than 1024. (7805 packets)

**Answer:** `tcp[0:2]>1024||udp[0:2]>1024`

---

## Question 4

**Prompt:** Capture all UDP protocol packets. (1277 packets)

**Answer:** `ip[9]=17||ip6[6]=17`

---

## Question 5

**Prompt:** Capture all packets with ACK/RST or ACK/FIN flags set. (1201 packets)

**Answer:** `tcp[13]=20||tcp[13]=17`

---

## Question 6

**Prompt:** Capture all packets with IP ID equal to 213. (10 packets)

**Answer:** `ip[4:2]=213||ip[0]=0`

---

## Question 7

**Prompt:** Capture all packets with a VLAN tag. (182 packets)

**Answer:** `ether[12:2]=33024`

---

## Question 8

**Prompt:** Capture all packets with DSCP value of 37. (42 packets)

**Answer:** `ip[1]=148`

> **Note:** Pending — variations listed. Current best answer: `ip[1]=148`

---

## Question 9

**Prompt:** Capture the start of a traceroute. (83 packets)

**Answer:** `ip[8]=1&&(ip[9]=1||ip[9]=17)`

---

## Question 10

**Prompt:** Capture all DNS packets. (63 packets)

**Answer:** [pending]

---

## Question 11

**Prompt:** Capture all TCP SYN initial connection packets. (3447 packets)

**Answer:** `tcp[13]=2`

---

## Question 12

**Prompt:** Capture all SYN-ACK responses indicating open ports. (277 packets)

**Answer:** `tcp[13]=18`

---

## Question 13

**Prompt:** Capture all RST responses indicating closed ports. (17 packets)

**Answer:** [pending]

---

## Question 14

**Prompt:** Capture all traffic on well known ports. (3678 packets)

**Answer:** [pending]

---

## Question 15

**Prompt:** Capture all HTTP traffic. (1404 packets)

**Answer:** [pending]

---

## Question 16

**Prompt:** Capture all Telnet traffic. (62 packets)

**Answer:** [pending]
