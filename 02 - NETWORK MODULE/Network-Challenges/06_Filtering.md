# Filtering Challenges

## Task 1 -- Host Filtering

### Question 01 -- IP/NFTables Filtering - T1

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> Configure iptables on T1 to allow specific traffic types, set default DROP policies, and verify connectivity.
> Once complete, open a netcat listener on Pivot at port 9001 and wait for the flag.
> If configuration is incorrect, the flag will not be received.

#### Steps Taken

1. Allowed TCP traffic for SSH (22), TELNET (23), HTTP (80), and RDP (3389):

```bash
sudo iptables -A INPUT -p tcp -m multiport --ports 22,23,80,3389 -j ACCEPT
sudo iptables -A OUTPUT -p tcp -m multiport --ports 22,23,80,3389 -j ACCEPT
```

2. Scheduled a reboot in 3 minutes as a failsafe before applying DROP policies:

```bash
sudo shutdown -r +3
```

3. Set default policies to DROP:

```bash
sudo iptables -P INPUT DROP
sudo iptables -P OUTPUT DROP
sudo iptables -P FORWARD DROP
```

4. Cancelled the reboot after confirming access was retained:

```bash
sudo shutdown -c
```

5. Allowed ICMP (ping) between T1 and Pivot (example IP: 10.10.0.40):

```bash
sudo iptables -A INPUT -p icmp --icmp-type 8 -s 10.10.0.40 -j ACCEPT
sudo iptables -A OUTPUT -p icmp --icmp-type 0 -d 10.10.0.40 -j ACCEPT
sudo iptables -A OUTPUT -p icmp --icmp-type 8 -d 10.10.0.40 -j ACCEPT
sudo iptables -A INPUT -p icmp --icmp-type 0 -s 10.10.0.40 -j ACCEPT
```

6. Allowed TCP and UDP traffic on ports 6579 and 4444:

```bash
sudo iptables -A INPUT -p tcp -m multiport --ports 6579,4444 -j ACCEPT
sudo iptables -A OUTPUT -p tcp -m multiport --ports 6579,4444 -j ACCEPT
sudo iptables -A INPUT -p udp -m multiport --ports 6579,4444 -j ACCEPT
sudo iptables -A OUTPUT -p udp -m multiport --ports 6579,4444 -j ACCEPT
```

7. Allowed HTTP traffic with state tracking:

```bash
sudo iptables -A INPUT -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
sudo iptables -A OUTPUT -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT
```

8. On Pivot, started netcat listener:

```bash
nc -lvp 9001
```

9. Waited for flag confirmation.

**Flag:** `467accfb25050296431008a1357eacb1`

---

### Question 02 -- IP/NFTables Filtering - T2

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> Configure nftables on T2 to allow specific traffic types, set default DROP policies, and verify connectivity.
> Once complete, open a netcat listener on Pivot at port 9002 and wait for the flag.
> If configuration is incorrect, the flag will not be received.

#### Steps Taken

1. Created input and output base chains with hook priority 0 and default policy accept:

```bash
sudo nft add table ip CCTC
sudo nft add chain ip CCTC INPUT { type filter hook input priority 0 \; policy accept \;}
sudo nft add chain ip CCTC OUTPUT { type filter hook output priority 0 \; policy accept \;}
```

2. Allowed NEW and ESTABLISHED traffic for SSH (22), TELNET (23), and RDP (3389):

```bash
sudo nft add rule ip CCTC INPUT tcp dport { 22, 23, 3389 } ct state { new, established } accept
sudo nft add rule ip CCTC OUTPUT tcp sport { 22, 23, 3389 } ct state { new, established } accept
sudo nft add rule ip CCTC OUTPUT tcp dport { 22, 23, 3389 } ct state { new, established } accept
sudo nft add rule ip CCTC INPUT tcp sport { 22, 23, 3389 } ct state { new, established } accept
```

3. Scheduled a reboot in 3 minutes as a failsafe before applying DROP policies:

```bash
sudo shutdown -r +3
```

4. Changed chain policies to DROP:

```bash
sudo nft add chain ip CCTC INPUT { \; policy drop \;}
sudo nft add chain ip CCTC OUTPUT { \; policy drop \;}
```

5. Cancelled the reboot after confirming access was retained:

```bash
sudo shutdown -c
```

6. Allowed ICMP (ping) between T2 and Pivot (IP: 10.10.0.40):

```bash
sudo nft add rule ip CCTC INPUT ip saddr 10.10.0.40 icmp type 8 accept
sudo nft add rule ip CCTC OUTPUT ip daddr 10.10.0.40 icmp type 0 accept
sudo nft add rule ip CCTC OUTPUT ip daddr 10.10.0.40 icmp type 8 accept
sudo nft add rule ip CCTC INPUT ip saddr 10.10.0.40 icmp type 0 accept
```

7. Allowed TCP and UDP traffic on ports 5050 and 5150 both ways:

```bash
sudo nft add rule ip CCTC INPUT udp dport { 5050, 5150 } accept
sudo nft add rule ip CCTC OUTPUT udp sport { 5050, 5150 } accept
sudo nft add rule ip CCTC OUTPUT udp dport { 5050, 5150 } accept
sudo nft add rule ip CCTC INPUT udp sport { 5050, 5150 } accept
sudo nft add rule ip CCTC INPUT tcp dport { 5050, 5150 } accept
sudo nft add rule ip CCTC OUTPUT tcp sport { 5050, 5150 } accept
sudo nft add rule ip CCTC OUTPUT tcp dport { 5050, 5150 } accept
sudo nft add rule ip CCTC INPUT tcp sport { 5050, 5150 } accept
```

8. Allowed HTTP traffic with state tracking:

```bash
sudo nft add rule ip CCTC INPUT tcp dport 80 ct state { new, established } accept
sudo nft add rule ip CCTC OUTPUT tcp sport 80 ct state { new, established } accept
sudo nft add rule ip CCTC OUTPUT tcp dport 80 ct state { new, established } accept
sudo nft add rule ip CCTC INPUT tcp sport 80 ct state { new, established } accept
```

9. Ran a rule flush failsafe in a separate terminal before applying DROP policies:

```bash
sleep 30 && nft flush ruleset
```

10. On Pivot, started netcat listener:

```bash
nc -lvp 9002
```

11. Waited for flag confirmation.

**Flag:** `9f7a33941828bdafd2755fd20176cdf4`

---

### Question 03 -- IP/IPTables Filtering - T3

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> Configure iptables on T3 to allow specific traffic types, set default DROP policies, and verify connectivity.
> Once complete, open a netcat listener on Pivot at port 9003 and wait for the flag.
> If configuration is incorrect, the flag will not be received.

#### Steps Taken

1. Allowed NEW and ESTABLISHED traffic for SSH (22), TELNET (23), HTTP (80), and RDP (3389):

```bash
sudo iptables -A INPUT -p tcp -m multiport --ports 22,23,80,3389 -j ACCEPT
sudo iptables -A OUTPUT -p tcp -m multiport --ports 22,23,80,3389 -j ACCEPT
```

2. Scheduled a rule flush failsafe in a separate terminal to prevent lockout:

```bash
sleep 30 && sudo iptables -P INPUT ACCEPT && sudo iptables -P FORWARD ACCEPT && sudo iptables -P OUTPUT ACCEPT
```

Alternatively:

```bash
sudo shutdown -r +3
sudo shutdown -c
```

3. Changed default policies to DROP for all chains:

```bash
sudo iptables -P INPUT DROP
sudo iptables -P OUTPUT DROP
sudo iptables -P FORWARD DROP
```

4. Cancelled chain reset after confirming access was retained (`CTRL+C`)

5. Allowed HTTP traffic with state tracking:

```bash
sudo iptables -A INPUT -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
sudo iptables -A OUTPUT -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT
```

6. On Pivot, started netcat listener:

```bash
nc -lvp 9003
```

7. Waited for flag confirmation.

**Flag:** `05e5fb96e2a117e01fc1227f1c4d664c`

---

### Question 04 -- IP/NFTables Filtering - Validation

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> This challenge verifies your IPTables and NFTables configurations across T1, T2, and T3.
> It is not required for the individual flags but is necessary to obtain the Validation Flag.
> Ensure connectivity and monitoring conditions are met before submitting the combined flag hash.

#### Host Information

| Host | Hostname | IP |
|------|----------|----|
| T1 | BLUE_Host-1 | 172.16.82.106 |
| T2 | BLUE_Host-3 | 172.16.82.112 |
| T3 | BLUE_INT_DMZ_HOST-1 | 172.16.40.10 |

#### Steps Taken

1. Verified Pivot can SSH into all three targets (T1, T2, T3)
2. Confirmed Pivot can ping T1 and T2, but not T3
3. Ensured T1, T2, and T3 cannot ping each other
4. Verified Pivot can access all three targets via HTTP
5. On T3, monitored external web traffic using TCPDump:

```bash
sudo tcpdump -i any port 80
```

6. On T1, monitored traffic on allowed high ports using TCPDump:

```bash
sudo tcpdump -i any port 6579 or port 4444
```

7. On T2, monitored traffic on allowed high ports using TCPDump:

```bash
sudo tcpdump -i any port 5050 or port 5150
```

8. Collected flags from each host:
   - T1 Flag: `467accfb25050296431008a1357eacb1`
   - T2 Flag: `9f7a33941828bdafd2755fd20176cdf4`
   - T3 Flag: `05e5fb96e2a117e01fc1227f1c4d664c`

9. On Pivot, generated md5sum of combined flags:

```bash
echo "467accfb25050296431008a1357eacb1_9f7a33941828bdafd2755fd20176cdf4_05e5fb96e2a117e01fc1227f1c4d664c" | md5sum
```

Result: `953e720e688941b15b72c098022c51c3`

10. Sent the hash to IP 172.16.1.15 on Port 5309 using Python socket:

```bash
python3 -c "import socket; s=socket.socket(); s.connect(('172.16.1.15', 5309)); s.sendall(b'953e720e688941b15b72c098022c51c3'); print(s.recv(1024).decode()); s.close()"
```

11. Received the Validation Flag from the server.

**Flag:** `d3b88e04de1e76482a1972f36734a7d8`

---

## Task 2 -- NAT

### Question 01 -- IPTables NAT Configuration (T5)

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> On T1, configure NAT to enable IP forwarding and translate traffic from T5.
> Once complete, open a netcat listener on Pivot at port 9004 and wait for the flag.
> If configuration is incorrect, the flag will not be received.

#### Steps Taken

1. Enabled IP forwarding on T1:

```bash
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
cat /proc/sys/net/ipv4/ip_forward
```

2. Set FORWARD policy to ACCEPT:

```bash
sudo iptables -P FORWARD ACCEPT
```

3. Configured NAT (POSTROUTING) to translate T5 traffic to T1's external interface:

```bash
sudo iptables -t nat -A POSTROUTING -s 192.168.1.10 -o eth0 -j MASQUERADE
```

4. Verified NAT rule with:

```bash
sudo iptables -t nat -L -n -v
```

5. On Pivot, started netcat listener:

```bash
nc -lvp 9004
```

6. Waited up to 2 minutes for flag confirmation.

**Flag:** `0c2ca80fad4accccce3bcecec1d238ce`

---

### Question 02 -- NFTables NAT Configuration (T6)

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> On T2, configure NAT using NFTables to enable IP forwarding and translate traffic from T6.
> Once complete, open a netcat listener on Pivot at port 9005 and wait for the flag.
> If configuration is incorrect, the flag will not be received.

#### Host Information

| Host | Hostname | IP |
|------|----------|----|
| T6 | BLUE_PRIV_HOST-3 | 192.168.3.30 |

#### Steps Taken

1. Enabled IP forwarding on T2:

```bash
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
cat /proc/sys/net/ipv4/ip_forward
```

2. Created NAT table and base chains:

```bash
sudo nft add table ip nat
sudo nft add chain ip nat PREROUTING '{ type nat hook prerouting priority 0; }'
sudo nft add chain ip nat POSTROUTING '{ type nat hook postrouting priority 0; }'
```

3. Configured POSTROUTING rule to translate T6 traffic to T2's external interface:

```bash
sudo nft add rule ip nat POSTROUTING ip saddr 192.168.3.30 oifname "eth0" masquerade
```

4. Verified NAT ruleset with:

```bash
sudo nft list table ip nat
```

5. On Pivot, started netcat listener:

```bash
nc -lvp 9005
```

6. Waited up to 2 minutes for flag confirmation.

**Flag:** `be33fe60229f8b8ee22931a3820d30ac`

---

### Question 03 -- NAT Validation

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> Validate NAT translations for T5 and T6.
> Confirm web access and Mission Command capability from both hosts.
> Once complete, generate an md5sum of both NAT flags separated by underscores to receive the NAT Validation Flag.
> After validation, delete and flush all NAT rules on T1 and T2.

#### Host Information

| Host | IP |
|------|----|
| T5 | 192.168.1.10 |
| T6 | 192.168.3.30 |

#### Steps Taken

1. Verified T5 can access the web and reach Mission Command
2. Verified T6 can access the web and reach Mission Command
3. Collected NAT flags:
   - NAT T5 Flag: `0c2ca80fad4accccce3bcecec1d238ce`
   - NAT T6 Flag: `be33fe60229f8b8ee22931a3820d30ac`

4. On Pivot, generated md5sum of combined flags:

```bash
echo "0c2ca80fad4accccce3bcecec1d238ce_be33fe60229f8b8ee22931a3820d30ac" | md5sum
```

Result: `e4f4c65b3884eadf7986adc76caea32c`

5. Deleted and flushed NAT rules on T1:

```bash
sudo iptables -t nat -F
sudo iptables -t nat -X
```

6. Deleted and flushed NAT rules on T2:

```bash
sudo nft flush table ip nat
sudo nft delete table ip nat
```

**Flag:** `e4f4c65b3884eadf7986adc76caea32c`

---

## Task 3 -- Snort IDS Signatures

### Confirm 1 -- Snort Passive Recon

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> Enumerate services on T4 to gain access, and perform Passive Recon.
> What command was used to run Snort on that machine?
> Provide exact syntax with associated options.

#### Host Information

- **T4:** Target system
- **Access Method:** `ssh -p 25 net7_student10@10.50.37.214`

#### Steps Taken

1. Connected to T4 via SSH
2. Ran process inspection command:

```bash
ps -elf | grep snort
```

3. Identified Snort running as root with the following command:

```bash
/usr/bin/snort -D -c /etc/snort/snort.conf
```

#### Option Breakdown

| Flag | Description |
|------|-------------|
| `-D` | Run Snort as a daemon (background process) |
| `-c` | Specify the configuration file to use |

**Answer:** `/usr/bin/snort -D -c /etc/snort/snort.conf`

---

### Confirm 2 -- Snort TCP Flag Alert

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> Utilizing T4, which SNORT rule would create an alert when No TCP Flags are set or the URG, PUSH, and FIN TCP Flags are set?
> Provide the full filename of the rule (not the entire path).

#### Steps Taken

1. Attempted to locate rules directory:
   - `cd /etc/ver/rules` -- Not found
   - `cd /etc/var/rules` -- Not found
   - `cd /etc/snort/rules` -- Success
2. Listed rule files:
   - Found: `dzt.rules`, `icmp.rules`, `lshell.rules`, `nm.rules`, `shell.rules`, `wbcm.rules`
3. Inspected `nm.rules` contents:

```
alert tcp any any -> any any (msg:"NULL Scan"; flags: 0;sid:9000007;)
alert tcp any any -> any any (msg:"XMAS Scan"; flags: FPU;sid:9000008;)
```

**Answer:** `nm.rules`

---

### Confirm 3 -- Snort NOP Sled Detection

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> Utilizing T4, which SNORT rule would create an alert when the Hex Indicator of a NOP Sled are detected?
> Provide the full filename of the rule (not the entire path).

#### Steps Taken

1. Verified active rule files via Snort configuration:

```bash
cat /etc/snort/snort.conf
```

Included: `icmp.rules`, `nm.rules`, `shell.rules`, `dzt.rules`

2. Searched for NOP sled hex pattern using grep:

```bash
grep -i '90 90' /etc/snort/rules/*
```

3. Found matching rule in `shell.rules`:

```
alert ip any any -> any any ( msg:"INDICATOR-SHELLCODE x86"; content:"|90 90 90 90 90 90 90 90 90 90 90 90 90 90|",fast_pattern,nocase; metadata:policy max-detect-ips drop,ruleset community; sid:648; rev:18; )
```

**Answer:** `shell.rules`

---

### Confirm 4 -- DNS Zone Transfer Detection

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> Utilizing T4, which SNORT rule would create an alert when a DNS Zone Transfer is detected with the content specified in CVE-1999-0532.
> Provide the full filename of the rule (not the entire path).

#### Steps Taken

1. Accessed T4 and navigated to Snort rules directory:

```bash
cd /etc/snort/rules
```

2. Searched for DNS zone transfer indicators related to CVE-1999-0532
3. Identified rule containing relevant content in `dzt.rules`
4. Rule matched zone transfer pattern and CVE reference

**Answer:** `dzt.rules`

---

### Confirm 5 -- ICMP Message Detection

| Field | Detail |
|-------|--------|
| **Level** | 1 |
| **Points** | 5 |

**Prompt:**
> Utilizing T4, which SNORT rule would create an alert when an ICMP Message is detected.
> Provide the full filename of the rule (not the entire path).

#### Steps Taken

1. Navigated to Snort rules directory:

```bash
cd /etc/snort/rules
```

2. Verified rule file inclusion via Snort config:

```bash
cat /etc/snort/snort.conf
```

Included: `icmp.rules`

3. Confirmed presence of ICMP detection rule in `icmp.rules`

**Answer:** `icmp.rules`

---

### ICMP 1 -- Default ICMP Rule Alert Count

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> From here on you will create your rules on either your Opstation or INTERNET-HOST.
> Using the provided Traffic Capture (/home/activity_resources/pcaps/ids.pcap), how many alerts are created with the default ICMP rule?

#### Resource Information

- **PCAP File:** `/home/activity_resources/pcaps/ids.pcap`
- **Optional Download:**

```bash
wget --no-check-certificate https://git.cybbh.space/net/public/raw/master/modules/networking/activities/resources/ids.pcap
```

#### Steps Taken

1. Downloaded the PCAP file to analysis host
2. Opened the file using Snort with default ICMP rule enabled
3. Filtered results for ICMP traffic
4. Matched alert count with displayed ICMP packets

**Answer:** `431`

---

### ICMP 2 -- Custom Rule Creation (cows.rules)

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> Utilizing your INTERNET_HOST, create a new rule called cows.rules.
>
> Rule Definition:
> - alert
> - any ICMP Messages Source to destination
> - Message = Cows
> - HEX content = DEADBEEF
> - Set sid to 1000001
>
> Provide the complete working rule that you created as the flag with NO SPACES between the ( ).
>
> To verify if your rule is correct:
> - `cat cows.rules | md5sum`
> - Confirmation MD5sum: `5e68869ecd077d427c182a392fa8b858`

#### Steps Taken

1. Created new rule file: `cows.rules`
2. Defined alert rule for ICMP traffic with HEX content match
3. Ensured formatting met flag requirements (no spaces between parentheses)
4. Verified rule integrity using md5sum

**Answer:**

```
alerticmpanyany->anyany(msg:"Cows";content:"|DEADBEEF|";sid:1000001;)
```

---

### ICMP 3 -- DEADBEEF Content Alert Count

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> Utilizing your INTERNET_HOST, and the provided Traffic Capture, how many alerts are created with the content of DEADBEEF?

#### Steps Taken

1. Verified `cows.rules` contains correct rule syntax:

```
alerticmpanyany->anyany(msg:"Cows";content:"|DEADBEEF|";sid:1000001;)
```

2. Ran Snort against the PCAP file using the custom rule:

```bash
sudo snort -r /home/student/ids.pcap -c /etc/snort/rules/cows.rules
```

3. Observed total alerts triggered by DEADBEEF content match

**Answer:** `80`

---

### ICMP 4 -- DMZ Ping Detection (dmz.rules)

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> Utilizing your INTERNET_HOST, create a new rule called dmz.rules.
>
> Rule Definition:
> - alert
> - any ICMP Echo Requests
> - Detects Type 8 / Code 0
> - Destination: 10.3.0.0/24
> - Message: DMZ Ping
> - SID: 1000002
>
> Provide the complete working rule that you created as the flag.

#### Steps Taken

1. Created new rule file: `dmz.rules`
2. Defined alert rule for ICMP Echo Request traffic to DMZ subnet
3. Specified ICMP Type 8 and Code 0
4. Verified rule syntax and SID assignment

**Answer:**

```
alerticmpanyany->10.3.0.0/24any(msg:"DMZ Ping";itype:8;icode:0;sid:1000002;)
```

---

### ICMP 5 -- DMZ Echo Request Alert Count

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> Utilizing your INTERNET_HOST, and the provided Traffic Capture, how many alerts are created for ICMP Echo Request messages to 10.3.0.0/24?

#### Command Used

```bash
sudo snort -r /home/student/ids.pcap -c /etc/snort/rules/dmz.rules
```

#### Steps Taken

1. Verified `dmz.rules` contains correct rule syntax:

```
alert icmp any any -> 10.3.0.0/24 any (msg:"DMZ Ping";itype:8;icode:0;sid:1000002;)
```

2. Ran Snort against the PCAP file using the DMZ rule:

```bash
sudo snort -r /home/student/ids.pcap -c /etc/snort/rules/dmz.rules
```

3. Observed total alerts triggered by ICMP Echo Requests to 10.3.0.0/24

**Answer:** `120`

---

### SSH Brute -- Threshold-Based Detection

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> Utilizing your INTERNET_HOST, create a new rule that will:
> - Track 3 SSH authentication attempts within 10 seconds
> - From a specific source
> - Using both threshold
>
> Utilizing the provided Traffic Capture, how many alerts are created for SSH Brute Force attempts?

#### Rule Definition

```
alert tcp any any -> any 22 (msg:"SSH Brute Force - 3 failed login attempts in 10s";threshold:type both, track by_src, count 3,seconds 10;sid:3000006;)
```

#### Steps Taken

1. Created new rule file: `sshbrute.rules`
2. Defined alert rule for TCP port 22 with SSH brute force threshold
3. Ran Snort against the PCAP file:

```bash
sudo snort -r /home/student/ids.pcap -c /etc/snort/rules/sshbrute.rules
```

4. Counted total alerts triggered by SSH brute force attempts

**Answer:** `9`

---

### RDP -- TCP/UDP Detection to Specific Host

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> Utilizing your INTERNET_HOST, create a new rule that will:
> - Identify traffic using both TCP and UDP Protocols
> - For RDP traffic going to 192.168.65.20
>
> Utilizing the provided Traffic Capture, how many alerts are created for RDP messages?

#### Rule Definitions

```
alert tcp any any -> 192.168.65.20 3389 (msg:"Show Me The Alerts";sid:1562345;)
alert udp any any -> 192.168.65.20 3389 (msg:"Show Me The Alerts";sid:1562346;)
```

#### Steps Taken

1. Created new rule file: `showme.rules`
2. Defined alert rules for both TCP and UDP RDP traffic to 192.168.65.20
3. Ran Snort against the PCAP file:

```bash
sudo snort -r /home/student/ids.pcap -c /etc/snort/rules/showme.rules
```

4. Counted total alerts triggered by RDP traffic

**Answer:** `234`

---

### Null Scan -- TCP Scan Detection

| Field | Detail |
|-------|--------|
| **Level** | 2 |
| **Points** | 10 |

**Prompt:**
> Utilizing your INTERNET_HOST, create a new rule that will:
> - Detect TCP Null scan to 10.3.0.0/24
> - Regardless of the traffic flow state
>
> Utilizing the provided Traffic Capture, how many alerts are created for TCP Null scan?

#### Rule Definition

```
alert tcp any any -> 10.3.0.0/24 any (msg:"SMBE BRUH";flags:0;sid:100123;)
```

#### Steps Taken

1. Created new rule file: `null.rules`
2. Defined alert rule for TCP traffic with no flags to 10.3.0.0/24
3. Ensured no flow state restrictions were applied
4. Ran Snort against the PCAP file:

```bash
sudo snort -r /home/student/ids.pcap -c /etc/snort/rules/null.rules
```

5. Counted total alerts triggered by TCP Null scan

**Answer:** `327`

---

### WannaCry -- SMB/CIFS Port-Based Detection

| Field | Detail |
|-------|--------|
| **Level** | 3 |
| **Points** | 15 |

**Prompt:**
> WannaCry ransomware and other Malware often use SMB and CIFS protocols as an attack vector for propagation.
> Identify the ports these protocols use.
> Utilizing your INTERNET_HOST, and the provided Traffic Capture:
> How many alerts are created for WannaCry using the Identified Ports regardless of the traffic flow state going to 10.0.0.0/8.

#### Identified Ports for SMB/CIFS

| Port | Protocol | Service |
|------|----------|---------|
| 445 | TCP | SMB over TCP |
| 139 | TCP | NetBIOS Session Service |
| 137 | UDP | NetBIOS Name Service |
| 138 | UDP | NetBIOS Datagram Service |

#### Rule Definitions

```
alert tcp any any -> 10.0.0.0/8 445 (msg:"MakeThemCry";sid:99997;)
alert udp any any -> 10.0.0.0/8 137 (msg:"MakeThemCry";sid:99998;)
alert tcp any any -> 10.0.0.0/8 139 (msg:"MakeThemCry";sid:99999;)
alert udp any any -> 10.0.0.0/8 138 (msg:"MakeThemCry";sid:99910;)
```

#### Steps Taken

1. Created new rule file: `cry.rules`
2. Defined alert rules for SMB/CIFS ports targeting 10.0.0.0/8
3. Ensured rules are protocol-specific and flow-agnostic
4. Ran Snort against the PCAP file:

```bash
sudo snort -r /home/student/ids.pcap -c /etc/snort/rules/cry.rules
```

5. Counted total alerts triggered by WannaCry-related traffic

**Answer:** `33`
