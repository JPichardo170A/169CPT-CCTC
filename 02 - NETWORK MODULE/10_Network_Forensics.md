# Network Forensics

## Network Forensics -- Mapping Techniques

- **Diagram Devices** -- Visually represent routers, hosts, switches, and firewalls.
- **Line Types** -- Use distinct styles for wired, wireless, or virtual links.
- **Written Information** -- Annotate diagrams with IPs, hostnames, roles, and protocols.
- **Coloring** -- Differentiate zones (e.g., DMZ, internal, external) or device types.
- **Groupings** -- Cluster by VLAN, subnet, or logical function.

**Purpose:** Build a clear and actionable picture of network layout and relationships.

---

## Network Forensics -- Device & Interface Details

When documenting network assets, capture the following for each device:

| Detail | Description |
|---|---|
| Device Type | Router, host, switch, etc. |
| System Hostnames | Label each system with its hostname |
| Interface Names | All interfaces (e.g., eth0, eth1, wlan0) |
| IP Address & CIDRs | IPs and subnet masks for each interface |
| TCP & UDP Ports | Open or listening ports per device |
| MAC Address | Hardware addresses for network interfaces |
| OS Type/Version | Operating system and version |
| Known Credentials | Discovered usernames/passwords for access |

---

## Network Mapping Tools -- Visualization Platforms

Recommended tools for building network diagrams:

- Draw.io Local (Template)
- Draw.io Web
- Witeboard.com
- Draw.Chat
- SmartDraw
- Ziteboard
- Tutorialspoint Whiteboard
- Explain Everything Whiteboard

---

## Network Recon Methodology -- Tactical Guidance

**Suggested Techniques:**
1. Start with passive discovery (logs, sniffers, ARP tables).
2. Use active scanning (ping, Nmap, /dev/tcp) to identify hosts and services.
3. Validate findings with banner grabbing and credential testing.
4. Document all assets and relationships in diagrams.
5. Use mapping tools to visualize and communicate findings.
6. Continuously update based on new intel or changes in the environment.

---

## Network Reconnaissance Completion -- Final Steps

- **Check on Learning** -- Review all mapped data and validate accuracy.
- **Intel Check** -- Investigate the CTF server or mission dashboard for new tasks.
- **Purpose:** Ensure all recon objectives are met and prepare for next phase.
