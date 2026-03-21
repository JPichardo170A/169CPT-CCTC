# Windows Boot Initialization - Challenges

Challenges covering the Windows boot process, kernel loading, safe mode, and BCDEdit.

---

## 1. What is the smallest addressable unit on a hard disk?

**Answer:** Sector

---

## 2. What is the first process that runs after the kernel loads?

**Answer:** System

---

## 3. What is the PID of the first Windows process?

**Answer:** 4

---

## 4. What is the second boot process that spawns csrss?

**Answer:** smss.exe

---

## 5. What is the Session ID for Windows services?

**Answer:** 0

---

## 6. Which process creates access tokens?

**Answer:** lsass.exe

---

## 7. What is the parent process to all svchosts?

**Answer:** services.exe

---

## 8. Which process waits for the Secure Attention Sequence?

**Answer:** winlogon.exe

---

## 9. What user space process spawns explorer then dies?

**Answer:** userinit.exe

---

## 10. What is the name of the bootloader (with extension)?

**Answer:** winload.exe

---

## 11. ONE attempt: BIOS or UEFI?

**Answer:** BIOS

---

## 12. What file saves the memory state for hibernation?

**Answer:** hiberfil.sys

---

## 13. What is the bootloader for restoring from hibernation?

**Answer:** winresume.exe

---

## 14. Find the flag from the bcdedit output in safe mode.

**Answer:**

```
bcdedit
```

**Flag:** `1RF5Zgf9P`

---

## 15. Fix safe mode and reboot. What is the flag?

**Steps:**

1. Launch the QEMU VM.
2. Run the following commands to remove safe mode settings:

```cmd
bcdedit /deletevalue {current} safeboot
bcdedit /deletevalue {current} safebootalternateshell
```

3. Reboot the system.

**Flag:** `76Drp6hB`
