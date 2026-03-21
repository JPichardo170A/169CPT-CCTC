# iptables Cheat Sheet

## iptables Command Syntax

| Component | Description |
|---|---|
| `iptables` | Main command-line utility |
| `-t [table]` | Table to use: `filter` (default), `nat`, `mangle`, `raw`, `security` |
| `[options]` | Operation to perform (e.g., `-A`, `-I`, `-D`, etc.) |
| `[chain]` | Chain to apply rule to: `INPUT`, `OUTPUT`, `FORWARD`, `PREROUTING`, `POSTROUTING` |
| `[rules]` | Matching criteria (e.g., IP, port, protocol) |
| `-j [action]` | Action to take: `ACCEPT`, `REJECT`, `DROP` |

---

## Common iptables Options

| Option | Description |
|---|---|
| `-A`, `--append` | Add rule to end of chain |
| `-I`, `--insert` | Insert rule at top or before rule number |
| `-R`, `--replace` | Replace rule at specified number |
| `-D`, `--delete` | Delete rule at specified number |
| `-F`, `--flush` | Remove all rules from specified table |
| `-L`, `--list` | List rules in table |
| `-S`, `--list-rules` | Show rules as interpreted commands |
| `-P`, `--policy` | Set default policy for chain |
| `-n`, `--numeric` | Show IPs and ports in numeric format |
| `--line-numbers` | Show rule numbers when listing |

---

## Rule Matching Syntax

| Rule Component | Description |
|---|---|
| `-i [iface]` | Input interface |
| `-o [iface]` | Output interface |
| `-s [IP/CIDR]` | Source IP or network |
| `-d [IP/CIDR]` | Destination IP or network |
| `-p [protocol]` | Protocol: `tcp`, `udp`, `icmp` |
| `--sport [port]` | Source port |
| `--dport [port]` | Destination port |
| `--icmp-type` | ICMP type and optional code |
| `--tcp-flags` | TCP flags to match |

---

## Match Extensions (-m)

| Extension | Description |
|---|---|
| `-m state` | Match connection state: `NEW`, `ESTABLISHED`, `RELATED`, etc. |
| `-m conntrack` | Similar to `state`, uses connection tracking |
| `-m mac` | Match MAC address: `--mac-source`, `--mac-destination` |
| `-m multiport` | Match multiple ports: `--ports`, `--dports`, `--sports` |
| `-m bpf` | Match using BPF bytecode |
| `-m iprange` | Match IP ranges: `--src-range`, `--dst-range` |

---

## Actions (-j)

| Action | Description |
|---|---|
| `ACCEPT` | Allow packet |
| `REJECT` | Deny packet and send ICMP response |
| `DROP` | Silently deny packet |

---

## Rule Examples

```bash
# Allow traffic on eth0
iptables -A INPUT -i eth0 -j ACCEPT

# Allow traffic to IP
iptables -A OUTPUT -d 10.10.0.40 -j ACCEPT

# Allow traffic to network
iptables -A INPUT -s 10.10.0.32/27 -j ACCEPT

# Allow SSH server port
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Allow DNS client port
iptables -A OUTPUT -p udp --dport 53 -j ACCEPT

# Allow ICMP echo request
iptables -A INPUT -p icmp --icmp-type 8 -j ACCEPT

# Allow multiple TCP ports
iptables -A INPUT -p tcp -m multiport --ports 21-23,80,3389 -j ACCEPT

# Allow TCP states
iptables -A INPUT -p tcp -m state --state NEW,ESTABLISHED -j ACCEPT
```

---

## Modify Rules

```bash
# Flush all rules
iptables -t filter -F

# Set default policy
iptables -t filter -P INPUT DROP

# List rules with numbers
iptables -t filter -L --line-numbers

# Show rules as commands
iptables -t filter -S

# Insert rule at top
iptables -t filter -I INPUT -p tcp --dport 22 -j ACCEPT

# Insert before rule #5
iptables -t filter -I INPUT 5 -p tcp --dport 22 -j ACCEPT

# Replace rule #5
iptables -t filter -R INPUT 5 -p tcp --dport 22 -j ACCEPT

# Delete rule #5
iptables -t filter -D INPUT 5
```
