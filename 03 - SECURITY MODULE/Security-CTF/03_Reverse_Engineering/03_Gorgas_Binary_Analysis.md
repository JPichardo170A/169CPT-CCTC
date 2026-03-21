# Gorgas Reverse Engineering - Binary Analysis

## Mission Briefing

Reverse engineering operation targeting binary executables.

- **Target:** 192.168.28.111
- **Path:** `/longTermStorage`
- **Tools:** Ghidra, IDA, GDB

---

## Scenario Flag

**Answer:** `R3V3R535t@rt0F@ct1v1ty`

---

## Binary Analysis — controller4.exe

### Approach

1. Loaded `controller4.exe` into Ghidra for disassembly.
2. Analyzed the decompiled output to identify encryption routines.
3. Identified XOR decryption logic applied to embedded data.
4. Traced seed generation used to initialize the decryption key.

### Key Findings

- The binary uses XOR-based decryption on embedded strings.
- Seed generation drives the key derivation process.
- Ghidra's decompiler view was essential for understanding the control flow.

---

## Verified Answer

**Answer:** `r3v3rs3m3_1s_s0_c0ol_1_l0v3_gh1dr4`

---

## Notes

- Ghidra is the primary tool for static analysis of this binary.
- Focus on XOR operations and seed values when analyzing the decryption routine.
- The flag is revealed after successfully reversing the decryption logic.
