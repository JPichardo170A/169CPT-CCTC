# Linux Boot Process - Challenges

Challenges covering hex math, MBR analysis, SysV Init, and SystemD.

---

## 1. What is the result of 0x31A - 0x21B?

**Answer:**

```
0x31A - 0x21B = 0xFF
```

In decimal: 794 - 539 = 255 = 0xFF

---

## 2. How many bits are in a nibble? How many bits are in a byte?

**Answer:**

```
Nibble = 4 bits
Byte = 8 bits
```

---

## 3. How many bits does a single hexadecimal character represent?

**Answer:**

```
4
```

One hex digit represents values 0-15, which requires 4 bits (one nibble).

---

## 4. How many bytes are in the range 0x00 to 0x10 (inclusive)?

**Answer:**

```
17
```

0x00 through 0x10 inclusive = 0 through 16 = 17 bytes.

---

## 5. What is the size of the MBR in bytes? What directory contains device files including disk devices?

**Answer:**

```
#512
/dev
```

The Master Boot Record is exactly 512 bytes and is located at the beginning of a disk device in `/dev`.

---

## 6. Which machine is the SysV Init system on?

**Answer:**

```
Minas_Tirith
```

---

## 7. What is the valid range of a single hexadecimal digit?

**Answer:**

```
0x0-0xF
```

A single hex digit ranges from 0 to F (0 to 15 in decimal).

---

## 8. What is the decimal range of a single hexadecimal digit?

**Answer:**

```
0-15
```

---

## 9. What is the result of 0x31A + 0x43?

**Answer:**

```
0x31A + 0x43 = 0x35D
```

In decimal: 794 + 67 = 861 = 0x35D

---

## 10. Examine the MBR of the disk. What are the first 8 hex byte values of the bootstrap code?

**Answer:**

```bash
xxd -l 8 /dev/sda
```

```
63,90,8e,d0,31,e4,8e,d8
```

---

## 11. What programming language is the MBR bootstrap code written in?

**Answer:**

```
Assembly
```

The MBR bootstrap code is written in x86 Assembly language.

---

## 12. Hash the first partition entry of the `mbroken` disk image. What is the MD5 hash?

**Answer:**

Extract the first partition entry (16 bytes starting at offset 0x1BE):

```bash
dd if=mbroken bs=1 skip=446 count=16 2>/dev/null | md5sum
```

```
2a5948fad4ec68170b23faaa2a16cef8
```

---

## 13. Find the string "GRUB" in the MBR and hash it. What is the MD5 hash?

**Answer:**

```bash
grep -boa "GRUB" /dev/sda | head -1
echo -n "GRUB" | md5sum
```

```
5fa690cb0f0789cbc57decfd096a503e
```

---

## 14. Hash the entire Bootstrap section of the MBR (first 446 bytes). What is the MD5 hash?

**Answer:**

```bash
dd if=/dev/sda bs=1 count=446 2>/dev/null | md5sum
```

```
d59a68c7b6d62ecaa1376dfb73a3b7be
```

---

## 15. What is the default run level on the SysV Init machine?

**Answer:**

```bash
cat /etc/inittab | grep initdefault
```

```
#2
```

The default run level is 2.

---

## 16. What is the meaning of existence? Find the script that runs at the end of all SysV run levels.

**Answer:**

Look at the final script executed across run levels:

```bash
ls /etc/rc6.d/
```

```
/etc/init.d/reboot
```

---

## 17. At which SysV run levels does the SSH daemon start?

**Answer:**

```bash
ls /etc/rc*.d/*ssh*
```

Look for symlinks starting with `S` (start):

```
2,3,4,5
```

---

## 18. On the SystemD machine, what does the `/sbin/init` symlink point to?

**Answer:**

```bash
ls -l /sbin/init
```

```
/lib/systemd/systemd
```

The traditional init path is a symlink to the SystemD binary.

---

## 19. What is the default SystemD target? What is its full file path?

**Answer:**

```bash
systemctl get-default
systemctl show -p FragmentPath default.target
```

```
graphical.target
/lib/systemd/system/graphical.target
```

---

## 20. What does the `graphical.target` unit Want?

**Answer:**

```bash
cat /lib/systemd/system/graphical.target
```

Look for the `Wants=` directive:

```
display-manager.service
```

---

## 21. Which dependency of `graphical.target` will block the boot process if it fails?

**Answer:**

Look for `Requires=` or hard dependencies in the unit file:

```bash
systemctl cat graphical.target
```

```
multi-user.target
```

If `multi-user.target` fails, `graphical.target` will not start.

---

## 22. How many direct `Wants` dependencies does the default target have (including wants directories)?

**Answer:**

```bash
systemctl show -p Wants graphical.target
ls /etc/systemd/system/graphical.target.wants/
```

Count all units listed in `Wants=` and the `.wants` directory:

```
7
```

---

## 23. What is the full path to the logging daemon binary (rsyslog)?

**Answer:**

```bash
which rsyslogd
systemctl cat rsyslog | grep ExecStart
```

```
/usr/sbin/rsyslogd
```

---

## 24. In the GRUB configuration, what command loads the kernel? What is the full path to the kernel image?

**Answer:**

```bash
cat /boot/grub/grub.cfg | grep vmlinuz
```

The GRUB command and kernel path:

```
linux
/boot/vmlinuz-4.9.0-16-amd64
```

The `linux` command in GRUB loads the specified kernel image into memory.
