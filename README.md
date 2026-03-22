<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0d1117,50:1a1f2e,100:00ff88&height=200&section=header&text=169CPT%20CCTC%20Repository&fontSize=36&fontColor=00ff88&fontAlignY=38&desc=Cyber%20Common%20Technical%20Core%20%7C%20Technical%20Labs%20%7C%20Operational%20Reference&descAlignY=58&descSize=16&descColor=8b949e" />

[![Author](https://img.shields.io/badge/WO1%20Pichardo-169th%20CPT-4B5320?style=for-the-badge&logoColor=white)](https://github.com/JPichardo170A)
[![Status](https://img.shields.io/badge/Status-Active-556B2F?style=for-the-badge)](.)
[![Updated](https://img.shields.io/badge/Updated-March%202026-36454F?style=for-the-badge)](.)

</div>

<br>

## About This Repository

This is a hands-on operational archive for the **Cyber Common Technical Core (CCTC)**, the technical backbone of Army cyber training. It contains lab walkthroughs, CTF challenge solutions, command references, and structured notes across OS fundamentals, networking, and security operations.

Designed for **17C** (Cyber Operations Specialists), **17A** (Cyber Operations Officers), and **170A** (Cyber Operations Technicians) working through CCTC or returning to reference material in the field.

> Looking for **WOBC course notes** (Python, PowerShell, DCO, Cloud)? See the companion repo:
>
> [![WOBC Notes](https://img.shields.io/badge/WOBC%20Notes-Course%20Material-4B5320?style=for-the-badge&logo=github&logoColor=white)](https://github.com/JPichardo170A/Warrant-Officer-Basic-Course-Notes)

<br>

## Module Index

<div align="center">

| # | Module | Topics Covered | Notes | Challenges |
|:-:|--------|----------------|:-----:|:----------:|
| `01` | [OS Module](01%20-%20OS%20MODULE/) | Windows & Linux fundamentals · Boot processes · Forensics · AD | 3 | 14 |
| `02` | [Network Module](02%20-%20NETWORK%20MODULE/) | OSI layers · Traffic analysis · Tunneling · Firewalls · Recon | 18 | 8 |
| `03` | [Security Module](03%20-%20SECURITY%20MODULE/) | Pen testing · Exploit dev · Reverse engineering · CTF walkthroughs | 7 | 17 |
| `04` | [SSH Tunneling Module](04%20-%20SSH%20TUNNELING%20MODULE/) | SSH tunneling labs (planned) | 0 | 0 |

</div>

<br>

## Tools Used

<div align="center">

![Kali Linux](https://img.shields.io/badge/Kali_Linux-36454F?style=flat-square&logo=kali-linux&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-36454F?style=flat-square&logo=linux&logoColor=white)
![Windows](https://img.shields.io/badge/Windows-36454F?style=flat-square&logo=windows&logoColor=white)
![Wireshark](https://img.shields.io/badge/Wireshark-36454F?style=flat-square&logo=wireshark&logoColor=white)
![Nmap](https://img.shields.io/badge/Nmap-36454F?style=flat-square&logo=nmap&logoColor=white)
![Ghidra](https://img.shields.io/badge/Ghidra-36454F?style=flat-square&logo=ghidra&logoColor=white)
![Metasploit](https://img.shields.io/badge/Metasploit-36454F?style=flat-square&logo=metasploit&logoColor=white)
![Bash](https://img.shields.io/badge/Bash-36454F?style=flat-square&logo=gnubash&logoColor=white)
![PowerShell](https://img.shields.io/badge/PowerShell-36454F?style=flat-square&logo=powershell&logoColor=white)

</div>

<br>

## Structure

Each module separates **notes** (reference material) from **challenges** (quizzes, CTFs, walkthroughs):

```
01 - OS MODULE/
├── Notes (boot processes, reference)
├── Windows-Challenges/    ← 10 quiz/CTF files with step-by-step solutions
└── Linux-Challenges/      ← 4 quiz/CTF files with step-by-step solutions

02 - NETWORK MODULE/
├── Notes (15 topic files + 2 reference sheets)
└── Network-Challenges/    ← BPF filter challenges + placeholders

03 - SECURITY MODULE/
├── Notes (6 files: pen testing, exploit dev, recon, GDB)
└── Security-CTF/          ← 17 CTF challenge walkthroughs
    ├── 01_Reconnaissance/
    ├── 02_Web_Exploitation/
    ├── 03_Reverse_Engineering/
    └── 04_Exploitation/
```

> **New here?** Each challenge subfolder has a `00 - README.md` index with a table of all challenges, topics, and flags.

<br>

## Official Course References

<div align="center">

| Resource | Description | Link |
|----------|-------------|------|
| CCTC OS Module | Operating systems curriculum | [View](https://os.cybbh.io/public/os/latest/index.html) |
| CCTC OS Portal | Full OS curriculum portal | [View](https://cted.cybbh.io/tech-college/cttsb/cctc/os/os/index.html) |
| CCTC Networking Module | Networking curriculum portal | [View](https://cted.cybbh.io/tech-college/cttsb/cctc/net/Net/) |
| CCTC Security Module | Security curriculum and op stations | [View](https://sec-cted-tech-college-cttsb-cctc-sec-12e0e75a1babbeec38083447b9.cybbh.io/) |

</div>

<br>

## Tools & Platforms

<div align="center">

| Tool | Purpose | Link |
|------|---------|------|
| PCTE Dashboard | Training modules and operational tools | [dash.ent1.pcte.mil](https://dash.ent1.pcte.mil/app/app) |
| CyberChef | Data encoding, decoding, and analysis | [gchq.github.io/CyberChef](https://gchq.github.io/CyberChef/) |
| Miro Main Board | Visual networking concepts and lab diagrams | [View Board](https://miro.com/app/board/o9J_klSqCSY=/?share_link_id=16) |

</div>

<br>

## CCTC Networking: Miro Section Boards

<div align="center">

> Instructor-maintained via third-party platform, not officially supported by CCTC. Use **Google Chrome** for best results.

| Day | Topic | Board |
|:---:|-------|:-----:|
| 1-2 | Network Fundamentals | [View](https://miro.com/app/board/uXjVL5Xh8-k=/?share_link_id=227946247596) |
| 3 | Traffic Capture | [View](https://miro.com/app/board/uXjVL4x9aPI=/?share_link_id=69452653029) |
| 4 | Socket Programming | [View](https://miro.com/app/board/uXjVL4wawbY=/?share_link_id=785182427927) |
| 5 | Network Reconnaissance | [View](https://miro.com/app/board/uXjVL4wUR4Q=/?share_link_id=922899713175) |
| 6 | File Transfer & Redirection | [View](https://miro.com/app/board/uXjVL4xZJ54=/?share_link_id=42990380004) |
| 7-8 | SSH Tunneling & Covert Channels | [View](https://miro.com/app/board/uXjVL4x58qg=/?share_link_id=164190037374) |
| 9 | Network Analysis | [View](https://miro.com/app/board/uXjVL4xYU9Q=/?share_link_id=148729317405) |
| 10-11 | Traffic Filtering | [View](https://miro.com/app/board/uXjVL43JXxg=/?share_link_id=404073487405) |

</div>

<br>

## Feedback & Suggestions

Your input directly improves these materials for current and future soldiers. Found an error in a walkthrough? Have a tool or technique worth adding? Want to see a topic covered differently?

<div align="center">

[![Discussions](https://img.shields.io/badge/Join%20the%20Discussion-4B5320?style=for-the-badge&logo=github&logoColor=white)](https://github.com/JPichardo170A/169CPT-CCTC/discussions)
[![Submit Feedback](https://img.shields.io/badge/Submit%20Feedback-556B2F?style=for-the-badge&logo=github&logoColor=white)](https://github.com/JPichardo170A/169CPT-CCTC/issues/new/choose)

| Channel | Use It For |
|---------|------------|
| [Discussions](https://github.com/JPichardo170A/169CPT-CCTC/discussions) | Questions, ideas, topic suggestions, and general conversation |
| [Feedback Form](https://github.com/JPichardo170A/169CPT-CCTC/issues/new/choose) | Content improvements, error reports, and specific suggestions |

</div>

<br>

## Contact

<div align="center">

**WO1 Jonathan Pichardo**
169th Cyber Protection Team · US Army Cyber Branch

[![Email](https://img.shields.io/badge/Army%20Email-Contact-36454F?style=for-the-badge&logo=gmail&logoColor=white)](mailto:jonathan.a.pichardo2.mil@army.mil)
[![GitHub](https://img.shields.io/badge/GitHub-JPichardo170A-4B5320?style=for-the-badge&logo=github&logoColor=white)](https://github.com/JPichardo170A)
[![WOBC Repo](https://img.shields.io/badge/WOBC%20Notes-Companion%20Repo-556B2F?style=for-the-badge&logo=github&logoColor=white)](https://github.com/JPichardo170A/Warrant-Officer-Basic-Course-Notes)

<br>

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:00ff88,50:1a1f2e,100:0d1117&height=100&section=footer" />

**Stay sharp. Stay curious. Lead with purpose.**

*Last Updated: March 2026*

</div>
