# Gorgas Reverse Engineering - Binary Analysis

## Mission Briefing

- **Level:** Mission Initialization
- **Type of Operation:** DEVOPS
- **Date:** XX SEP 2025
- **Start Time:** 1300
- **Duration:** 4 hours
- **Objective:** Extract and analyze binaries hosted on Donovian_Webserver at /longTermStorage
- **Scenario Flag:** `R3V3R535t@rt0F@ct1v1ty`
- **Approved Tools/Techniques:**
  - Ghidra
  - IDA Pro
  - GDB
- **Connection Methods:**
  - Web access via port 80
  - SSH (if applicable)
- Extraction and reverse engineering permitted only on authorized Analyst Workstations

### Prior Approvals

- Extraction authorized from host: `Donovian-Webserver`
- Tasks limited to Analyst Workstations only

### Scheme of Maneuver

```
> Analyst Workstation
-> T1: 192.168.28.111
```

### Target Section

| Field | Value |
|-------|-------|
| **Hostname** | `web.site.donovia` |
| **IP Address** | `192.168.28.111` |
| **OS** | Unknown |
| **Credentials** | `comrade::StudentWebExploitPassword` |
| **Last Known SSH Port** | Unknown |
| **Action** | Extract approved binaries under "longTermStorage" and perform reverse engineering analysis |

---

## Binary Analysis Challenge

- **Level:** Intermediate
- **Points:** TBD

### Prompt

Analyze the binary `controller4.exe` extracted from Donovian development networks. Identify weaknesses and extract the symmetric key used during decryption of the payload. Submit the symmetric key in ASCII format.

---

### Reference Intelligence

- Disassembly of `FUN_140001370` and `FUN_140001090`
- Symbol resolution of `s_17_66_86_76_64_19_8_25_22_85_71_2_1400200a0`
- Control flow tracing from string loading to XOR decryption and file output
- Analysis of seed generation and character-wise XOR logic

---

### Steps Taken

1. Opened the binary in Ghidra and navigated to memory address `0x1400200a0`
2. Identified a space-separated numeric string used as input to the decryption function
3. Traced the call from `FUN_140001370` to `FUN_140001090`, which performs XOR-based decryption
4. Analyzed seed generation: a loop multiplies `0x1142b9` by `0x2773f` 101 times, then converts the result to a string
5. Reconstructed the XOR logic in Python: each number is XORed with a character from the seed string
6. Used the full numeric string:

```
17 66 86 76 64 19 8 25 22 85 71 22 92 67 90 86 81 30 77 83 80 66 30 119 91 93 65 92 95 116 21 17 92 67 90 86 81 23 69 120 65 17
```

7. Decoded the result to ASCII, confirming the output written to `secret3.ps1` is the symmetric key

---

### Verified Answer

**Flag:** `r3v3rs3m3_1s_s0_c0ol_1_l0v3_gh1dr4`

---

### Notes

- The numeric string is not the key itself, but an encrypted representation
- The key is revealed only after XORing each number with the derived seed string
- Ghidra's Listing View and Decompiler were essential for tracing memory and logic
- The decrypted output is written to `secret3.ps1`, confirming its role as the symmetric key
- The string `17_66_86_76_64_19_8_25_22_85_71_2_14` is a label, not the key
