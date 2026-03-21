# Passive External Discovery

## Network Reconnaissance -- Methods Used for Discovery

### Outcomes

- Define Passive Network Information Gathering
- Define Network Collection Devices and Relevant Data
- Discuss Passive Network Analysis
- Use Network Analysis Tool
- Discuss Active Network Reconnaissance
- Perform Scans and Analyze Results
- Discuss Network Artifact Correlation
- Complete Network Reconnaissance

---

## Reconnaissance Stages

**Passive External** -- Gathering information from public sources without interacting with the target system.
- Tools/Methods: WHOIS, DIG, Google Dorking, SHODAN, PDF metadata, Social Tactics
- Targets: IP addresses, subdomains, technologies, people, historical content

**Active External** -- Direct interaction with external-facing systems to probe for data.
- Tools/Methods: NMAP, Netcat, Banner Grabbing, PING, Curl/Wget, /dev/tcp
- Targets: Open ports, services, banners, network reachability

**Passive Internal** -- Monitoring internal network traffic and system data without injecting packets.
- Tools/Methods: Netstat/SS, ARP cache, Routing Table, Packet Sniffing, System DNS
- Targets: Local processes, IP configurations, service files, OS location

**Active Internal** -- Actively probing internal systems for vulnerabilities or misconfigurations.
- Tools/Methods: NMAP, Netcat, PING, /dev/tcp, Banner Grabbing
- Targets: Internal ports, services, network topology, system responses

---

## Blue / Gray / Red Space -- Cyber Operations Framework

**Blue Space** -- The defender's environment; includes internal networks, systems, and users.
- Example: Corporate infrastructure, user endpoints, internal security tools
- Role: Initiates actions, defends assets, monitors internal activity

**Gray Space** -- The intermediary zone; includes public infrastructure like the Internet, DNS, and external proxies.
- Example: Cloud services, public DNS servers, VPNs, third-party platforms
- Role: Bridge between Blue and Red spaces; often used for staging or obfuscation

**Red Space** -- The adversary's environment; includes target systems, internal proxies, and assets.
- Example: Internal servers, databases, restricted networks, compromised endpoints
- Role: Final destination for offensive operations; where exploitation and pivoting occur

**Flow of Action:**
- Blue initiates reconnaissance or attack.
- Gray facilitates indirect access (e.g., via proxies or DNS).
- Red is where the target resides and actions are executed (e.g., pivoting, data exfiltration).

---

## Reconnaissance Steps -- Methodical Approach

### 1. Network Footprinting
- Initial phase focused on gathering publicly available information about the target.
- Targets: Network architecture, systems in use, organizational structure
- Methods: OSINT, WHOIS, DNS records, social media, company websites

### 2. Network Scanning
- Identifying live hosts, open ports, and services running on target systems.
- Techniques:
  - Port Scanning: Detect open/closed ports (e.g., using NMAP)
  - Network Scanning: Discover active devices and IP ranges
  - Vulnerability Scanning: Identify known flaws in services or configurations

### 3. Network Enumeration
- Extracting detailed information from discovered systems.
- Targets: Network resources, users and groups, routing tables, auditing and service settings, machine names and banners, SNMP and DNS details
- Tools: SNMPwalk, Netstat, enum4linux, rpcinfo, nslookup

### 4. Vulnerability Assessment
- Evaluating systems for exploitable weaknesses.
- Categories: Injection, Broken Authentication, Sensitive Data Exposure, XXE, Broken Access Control, Security Misconfiguration, Software with Known Vulnerabilities
- Tools: Nessus, OpenVAS, Burp Suite, Nikto

---

## Methods Used for Passive External Discovery

- **WHOIS** -- Retrieves domain registration details including owner, registrar, and contact info.
- **DIG** -- Queries DNS records to uncover domain structure, IP addresses, and mail servers.
- **Zone Transfers** -- Attempts to replicate DNS zone data; useful for mapping subdomains.
- **Netcraft** -- Reveals server technologies, hosting providers, and historical infrastructure data.
- **Historical Content** -- Uses tools like Wayback Machine to analyze past versions of websites.
- **Google Dorking** -- Advanced search operators to uncover sensitive or indexed data.
- **SHODAN** -- Scans the internet for exposed devices, services, and misconfigured systems.
- **PDF Metadata** -- Extracts hidden data from documents using tools like ExifTool.
- **Social Tactics** -- Profiling individuals or organizations via LinkedIn, GitHub, Twitter, etc.
- **Passive OS Fingerprinting** -- Tools like `p0f` identify operating systems without active probing.

---

## Sock Puppet Creation -- OSINT Persona Building

1. **Define Persona** -- Choose a realistic identity that fits your investigative goals.
2. **Generate Identity** -- Use tools like Fake Name Generator, This Person Does Not Exist, ElfQrin.
3. **Build Infrastructure** -- Set up email (ProtonMail/Fastmail), phone (Mint Mobile/Silent Link), VPN matching persona's geography.
4. **Create Accounts** -- Register on relevant platforms (Twitter, LinkedIn, GitHub, Reddit, forums, cloud services).
5. **Populate Profile** -- Add minimal but realistic content (bio, profile picture, interests, posts).
6. **Maintain Anonymity** -- Avoid linking to real accounts or IPs. Use clean browsers, disable autofill, rotate VPNs.
7. **Stay Active** -- Log in periodically, engage lightly, avoid suspicious inactivity.

---

## Useful Sites -- OSINT Investigation Resources

- **OSINT Framework** -- Categorized collection of OSINT tools and techniques. Website: osintframework.com
- **Malfrat's OSINT Map** -- Interactive map of OSINT tools. Website: https://map.malfrats.industries/
- **Mark@OSINT-Research** -- Curated pages and blog posts on advanced OSINT tactics.
- **Pentest-Standard** -- Structured methodology for penetration testing.
- **SecuritySift** -- Educational blog focused on penetration testing and network analysis.

---

## Passive Recon Activities -- OSINT & PAI Techniques

### Open-Source Intelligence (OSINT)
- Gathering data from publicly accessible sources without interacting with the target.
- Examples: Social media profiles, GitHub repositories, company websites, news articles

### Publicly Available Information (PAI)
- Includes government records, technical documentation, and public datasets.
- Examples: SEC filings, job postings, public forums, research papers

### IP Addresses and Sub-domains

**IP Registries:**
- [IANA IPv4](https://www.iana.org/assignments/ipv4-address-space/ipv4-address-space.xhtml)
- [IANA IPv6](https://www.iana.org/assignments/ipv6-address-space/ipv6-address-space.xhtml)

**DNS Lookups:**
- [ARIN](https://www.arin.net)
- [DomainTools WHOIS](https://whois.domaintools.com)
- [ViewDNS](https://viewdns.info)
- [DNSDumpster](https://dnsdumpster.com)
- [CentralOps](https://centralops.net)

**URL Scan:**
- [Netcraft Site Report](https://sitereport.netcraft.com)
- [Web-Check XYZ](https://web-check.xyz)
- [Web-Check AS93](https://web-check.as93.net)
- [URLScan](https://urlscan.io)

**IP Geolocation Lookup:**
- [MaxMind](https://www.maxmind.com)
- [IP Location IO](https://www.iplocation.io)
- [IP Location Net](https://www.iplocation.net)
- [InfoSniper](https://www.infosniper.net)

**BGP Prefixes:**
- [BGPView](https://bgpview.io)
- [HackerTarget](https://hackertarget.com)
- [Hurricane Electric BGP](https://bgp.he.net)
- [BGP4.AS](https://bgp4.as)

---

## Discovery Techniques

### External Sites
- **Parent/Subordinate Organizations** -- Use corporate registries, WHOIS, and business directories.
- **Clients/Customers** -- Analyze testimonials, case studies, and CRM leaks.
- **Service Organizations** -- Identify hosting, cloud, and SaaS providers via Netcraft or Shodan.
- **Partners** -- Look for affiliate links, shared infrastructure, or joint press releases.

### Identifying People
- Target Website -- Scrape team pages, blog authors, and contact forms.
- Crawler Tools -- Use Maltego or Creepy for relationship mapping.
- Search Engines -- Google, Bing, DuckDuckGo with advanced operators.
- Social Media -- LinkedIn, Twitter, GitHub for professional and behavioral profiling.
- Job Portals -- Glassdoor, Indeed, LinkedIn Jobs for role-based insights.
- Tracking Active Emails -- Validate emails via Hunter.io or verify MX records.

### Identifying Technologies
- File Extensions -- `.php`, `.asp`, `.jsp`, `.config`, `.bak` indicate tech stack and exposure.
- Server Responses -- Analyze HTTP headers for server type, version, and tech hints.
- Job Listings -- Reveal internal tools, frameworks, and infrastructure.
- Website Content -- Use Wappalyzer or BuiltWith to fingerprint technologies.
- Google Hacking -- Use Google Dorks to uncover sensitive files.
- Shodan.io -- Scan for exposed services, devices, and misconfigurations.
- MAC OUI Lookup -- Identify vendor from MAC prefix.

```bash
# MAC OUI and EUI-64 Lookup Script
wget https://git.cybbh.space/cted/tech-college/cttsb/cctc/net/Net/-/raw/main/Resources/packages_and_scripts/mac_oui_lookup.py
chmod +x mac_oui_lookup.py
```

### Identifying Content of Interest
- Credential Files -- `/etc/passwd`, `/etc/shadow`, Windows SAM database
- Configuration Files -- `.conf`, `.ini`, `.yaml` often contain credentials or paths
- Log Files -- Reveal system behavior, errors, and user activity
- Backup Files -- `.bak`, `.zip`, `.tar.gz` may expose sensitive data
- Test Pages -- Unlinked or dev pages with debug info
- Client-Side Code -- JavaScript reveals API endpoints, logic, and tokens

### Identifying Vulnerabilities
- Known Technologies -- Cross-reference with CVEs and vendor advisories.
- Error Message Responses -- Analyze verbose errors for stack traces or misconfigurations.
- Identify Running Services -- Use Nmap, banner grabbing, and service fingerprinting.
- Identify Running OS -- Use TTL values, TCP/IP stack behavior, or tools like p0f.
- Monitor Running Applications -- Use Shodan, Netcraft, or passive DNS to track exposed apps.

---

## Compliance Auditing

- **PCI DSS and GLBA** -- Protect cardholder data and financial privacy; required for financial institutions and merchants.
- **NERC** -- North American Electric Reliability Corporation standards for securing bulk electric systems.
- **FISMA** -- Federal Information Security Management Act; mandates security controls for federal agencies.
- **SOX** -- Sarbanes-Oxley Act; ensures integrity of financial reporting and internal controls.
- **HIPAA** -- Health Insurance Portability and Accountability Act; protects patient health information.

---

## DNS Intelligence Tools

### WHOIS
- Queries DNS registrar over TCP port 43 to retrieve domain registration details.
- Reveals domain owner, registrar, contact info, and creation/expiration dates.

```bash
whois zonetransfer.me
```

### DIG
- Queries DNS servers over UDP port 53 to resolve domain names and retrieve DNS records.

```bash
dig zonetransfer.me A        # IPv4 address
dig zonetransfer.me AAAA     # IPv6 address
dig zonetransfer.me MX       # Mail servers
dig zonetransfer.me TXT      # Text records
dig zonetransfer.me NS       # Name servers
dig zonetransfer.me SOA      # Start of Authority record
```

### Zone Transfer
- Transfers full DNS zone data between primary and secondary servers over TCP port 53.
- Used for replication but can leak sensitive data if misconfigured.

```bash
dig axfr @nsztm1.digi.ninja zonetransfer.me
```

Reference: https://digi.ninja/projects/zonetransferme.php

### Netcraft
- Web-based tool similar to WHOIS; provides hosting history, technologies, and server fingerprinting.
- Website: https://sitereport.netcraft.com

---

## Reconnaissance Tools

### Shodan
- A search engine for discovering Internet-connected devices and services.
- Reveals exposed systems such as webcams, routers, ICS, and servers.
- Website: https://www.shodan.io

### p0f -- Passive OS Fingerprinter
- Tool for passive OS fingerprinting using live traffic or packet captures.
- Does not send packets; analyzes existing traffic to infer OS.

```bash
# View fingerprint file
more /etc/p0f/p0f.fp

# Live interface scan
sudo p0f -i eth0

# PCAP file analysis
sudo p0f -r test.pcap
```

---

## Social Tactics -- Human-Centric Reconnaissance

### Social Engineering
- Psychological manipulation to trick individuals into revealing sensitive information.
- Targets emotions like trust, fear, urgency, and curiosity.

### Technical-Based Attacks
- Email: Phishing, spoofing, malicious attachments
- SMS: Smishing attacks with fake alerts or links
- Bluetooth: Bluejacking or exploiting insecure pairing

### Physical-Based Attacks
- Dumpster Diving: Retrieving sensitive documents from trash
- Shoulder Surfing: Observing screens or keystrokes in public spaces
- Tailgating: Following authorized personnel into restricted areas
