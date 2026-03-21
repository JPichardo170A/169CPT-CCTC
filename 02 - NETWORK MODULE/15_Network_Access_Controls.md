# Network Access Controls

## Outcomes

- Describe Access Control Lists
- Demonstrate ACL Implementation
- Standard vs Extended ACL capabilities
- ACL Placement strategies
- VTY Access Control
- NAT Integration
- QoS Classification

---

## Common ACL Methods

| Method | Description |
|---|---|
| Standard ACLs | Filter based on source IP address only; numbered 1-99, 1300-1999 |
| Extended ACLs | Filter based on source/dest IP, protocol, ports; numbered 100-199, 2000-2699 |
| Named ACLs | Use descriptive names instead of numbers |
| Interface ACLs | Applied to router/switch interfaces for traffic control |
| VTY ACLs | Restrict remote management access via Telnet/SSH |
| NAT ACLs | Define traffic eligible for Network Address Translation |
| QoS ACLs | Classify traffic for Quality of Service policies |

---

## Standard ACL Configuration

### Overview

- **Numbers:** 1-99, 1300-1999
- **Filtering:** Source IP address only
- **Placement:** Near destination (best practice)
- **Processing:** Top-down, first match wins
- **Default:** Implicit deny any at end
- **Wildcards:** Inverse subnet mask (0.0.0.255 = /24)

### Numbered Standard ACL

```
Router(config)# access-list 10 permit 192.168.1.0 0.0.0.255
Router(config)# access-list 10 deny any
Router(config)# interface GigabitEthernet0/1
Router(config-if)# ip access-group 10 in
```

### Host-Specific Example

```
Router(config)# access-list 15 permit host 10.1.1.100
Router(config)# access-list 15 deny 10.1.1.0 0.0.0.255
Router(config)# access-list 15 permit any
```

### Named Standard ACL

```
Router(config)# ip access-list standard ADMIN_ONLY
Router(config-std-nacl)# permit 192.168.10.0 0.0.0.255
Router(config-std-nacl)# deny any
Router(config)# interface FastEthernet0/0
Router(config-if)# ip access-group ADMIN_ONLY out
```

---

## Extended ACL Configuration

### Overview

- **Numbers:** 100-199, 2000-2699
- **Filtering:** Source/destination IP, protocol, ports
- **Placement:** Near source (best practice)
- **Transport:** TCP, UDP, ICMP, IP (any)
- **Operators:** eq, gt, lt, neq, range

### TCP Traffic Control

```
# Web Server Access
Router(config)# access-list 101 permit tcp any host 192.168.1.100 eq 80
Router(config)# access-list 101 permit tcp any host 192.168.1.100 eq 443
Router(config)# access-list 101 deny ip any any

# SSH Management Access
Router(config)# ip access-list extended SSH_MGMT
Router(config-ext-nacl)# permit tcp 10.1.1.0 0.0.0.255 any eq 22
Router(config-ext-nacl)# deny tcp any any eq 22
Router(config-ext-nacl)# permit ip any any
```

### UDP Traffic Control

```
# DNS Resolution
Router(config)# access-list 102 permit udp any any eq 53
Router(config)# access-list 102 permit udp any eq 53 any

# DHCP Services
Router(config)# access-list 103 permit udp any any eq 67
Router(config)# access-list 103 permit udp any any eq 68
```

### ICMP Control

```
Router(config)# access-list 104 permit icmp any any echo
Router(config)# access-list 104 permit icmp any any echo-reply
Router(config)# access-list 104 deny icmp any any
```

---

## Specialized ACL Applications

### VTY Line Access Control

```
Router(config)# access-list 20 permit 10.1.1.0 0.0.0.255
Router(config)# access-list 20 permit host 192.168.100.50
Router(config)# line vty 0 15
Router(config-line)# access-class 20 in
Router(config-line)# transport input ssh
Router(config-line)# login local
```

### NAT Access Control Lists

```
# Inside source translation
Router(config)# access-list 1 permit 192.168.0.0 0.0.255.255
Router(config)# access-list 1 permit 10.0.0.0 0.255.255.255
Router(config)# ip nat inside source list 1 interface G0/0 overload

# Static NAT with ACL
Router(config)# access-list 2 permit host 192.168.1.100
Router(config)# ip nat inside source list 2 pool PUBLIC_POOL

# Policy NAT example
Router(config)# access-list 110 permit tcp 192.168.1.0 0.0.0.255 any eq 80
Router(config)# route-map WEB_NAT permit 10
Router(config-route-map)# match ip address 110
```

### Quality of Service ACLs

```
# Traffic classification
Router(config)# access-list 101 permit tcp any any eq 80
Router(config)# access-list 101 permit tcp any any eq 443
Router(config)# class-map match-all WEB_TRAFFIC
Router(config-cmap)# match access-group 101

# Voice traffic priority
Router(config)# access-list 102 permit udp any any range 16384 32767
Router(config)# class-map match-all VOICE_TRAFFIC
Router(config-cmap)# match access-group 102
Router(config)# policy-map QOS_POLICY
Router(config-pmap)# class VOICE_TRAFFIC
Router(config-pmap-c)# priority percent 30
```

---

## IDS/IPS & Snort Rules

### Outcomes

- Describe Intrusion Detection methods
- Demonstrate Snort Rules
- Rule Structure components
- Payload Analysis techniques
- Traffic Analysis methods
- Threshold Management
- Response Actions

---

## Common IDS/IPS Methods

| Method | Description |
|---|---|
| Signature-Based | Pattern matching against known attack signatures |
| Anomaly-Based | Statistical analysis to detect unusual behavior patterns |
| Protocol Analysis | Deep inspection of protocol compliance and anomalies |
| Heuristic Analysis | Rule-based logic to identify suspicious activities |
| Hybrid Detection | Combination of multiple detection techniques |
| Network-Based (NIDS) | Monitors network traffic in real-time |
| Host-Based (HIDS) | Monitors individual system activities and files |

---

## Snort Rule Configuration

### Rule Structure

```
<action> <protocol> <src_ip> <src_port> <direction> <dst_ip> <dst_port> (options)
```

- **Actions:** alert, log, pass, drop, reject, sdrop
- **Protocols:** tcp, udp, icmp, ip
- **Directions:** `->` (unidirectional), `<>` (bidirectional)
- **Options:** Enclosed in parentheses, semicolon-terminated
- **Processing:** First match wins, order matters
- **SID Range:** 1-999999 (reserved), 1000000+ (local rules)

### Basic Alert Rules

```
# Web server access attempt
alert tcp any any -> 192.168.1.0/24 80 (msg:"Web server access attempt"; sid:1000001; rev:1;)

# SSH connection attempt
alert tcp any any -> any 22 (msg:"SSH connection attempt"; sid:1000002; rev:1;)

# ICMP flood attempt
alert icmp any any -> 192.168.1.0/24 any (msg:"ICMP flood attempt"; sid:1000003; rev:1;)
```

### Advanced Rules

```
# FTP anonymous login
alert tcp any any -> any 21 (msg:"FTP anonymous login attempt";
    content:"USER anonymous"; nocase;
    flow:to_server,established;
    sid:1000004; rev:1;)

# SQL injection attempt
alert tcp any any -> any 80 (msg:"SQL injection attempt detected";
    content:"SELECT"; nocase;
    content:"FROM"; distance:0; within:100; nocase;
    flow:to_server,established;
    sid:1000005; rev:2;)
```

---

## Snort Rule Options -- Content Detection

### Basic Content Matching

```
content:"<string>";              # Exact string match
nocase;                          # Case insensitive matching
```

### Advanced Content Options

```
depth:<bytes>;                   # Search within first N bytes
offset:<position>;               # Start search at specific position
distance:<bytes>;                # Search N bytes after previous match
within:<bytes>;                  # Search within N bytes of previous match
```

### Example

```
alert tcp any any -> any 80 (msg:"Credit card number detected";
    content:"4"; depth:1;
    pcre:"/4\d{3}[\s-]?\d{4}[\s-]?\d{4}[\s-]?\d{4}/";
    sid:1000006;)
```

---

## Snort Rule Options -- Flow & Protocol Analysis

### Flow States

```
flow:to_server,established;      # Client to server, established connection
flow:to_client,established;      # Server to client, established connection
flow:from_server,established;    # From server, established connection
flow:from_client,established;    # From client, established connection
```

### TCP-Specific Options

```
flags:S;                         # SYN flag set
flags:SA;                        # SYN+ACK flags set
flags:F;                         # FIN flag set
flags:R;                         # RST flag set
```

### General Protocol Options

```
dsize:>100;                      # Payload size greater than 100 bytes
ttl:64;                          # Time to Live value
id:12345;                        # IP identification field
```

### Port Scan Detection Example

```
alert tcp any any -> 192.168.1.0/24 any (msg:"TCP port scan detected";
    flags:S; dsize:0;
    detection_filter:track by_src, count 10, seconds 5;
    sid:1000007;)
```

---

## Snort Rule Options -- Threshold Management

### Threshold Types

- **threshold:** Alert on Nth occurrence within time window
- **limit:** Alert once per time window after threshold met
- **both:** Combine threshold and limit behaviors

### Syntax Templates

```
threshold:type threshold, track by_src, count 5, seconds 60;
detection_filter:track by_src, count 3, seconds 30;
```

### Suppression

```
suppress gen_id 1, sig_id 1000001, track by_src, ip 192.168.1.100;
```

### Brute Force Detection Example

```
alert tcp any any -> any 22 (msg:"SSH brute force attack";
    content:"SSH-"; depth:4;
    detection_filter:track by_src, count 5, seconds 60;
    threshold:type limit, track by_src, count 1, seconds 300;
    sid:1000008;)
```

---

## Rule Performance & Best Practices

- Place most specific content matches first in rule options
- Use fast_pattern keyword on most unique content
- Minimize regular expression usage -- prefer content when possible
- Apply flow state checking to reduce false positives
- Use detection filters on high-volume rules
- Order rules from most specific to most general
- Use meaningful SID ranges for rule organization
- Include descriptive messages for alert analysis
- Test rules in alert mode before implementing blocking
- Regular review and tuning of threshold settings
