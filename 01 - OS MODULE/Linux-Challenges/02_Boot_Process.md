# Linux Boot Process - Challenges

Challenges covering hex math, MBR analysis, SysV Init, and SystemD.

---

## 1. What is the result of 0x31A - 0x21B?

### [Step-by-Step Breakdown]

1. Convert each hex value to decimal:
   - `0x31A` = (3 x 256) + (1 x 16) + (10 x 1) = 768 + 16 + 10 = **794**
   - `0x21B` = (2 x 256) + (1 x 16) + (11 x 1) = 512 + 16 + 11 = **539**
2. Subtract in decimal: 794 - 539 = **255**
3. Convert result back to hex:
   - 255 / 16 = 15 remainder 15
   - 15 in hex = `F`, 15 in hex = `F`
   - 255 decimal = `0xFF`

**Answer:**

```
0x31A - 0x21B = 0xFF
```

In decimal: 794 - 539 = 255 = 0xFF

---

## 2. How many bits are in a nibble? How many bits are in a byte?

### [Step-by-Step Breakdown]

1. **Bit** -- The smallest unit of data in computing. A single binary digit: 0 or 1.
2. **Nibble** -- A group of **4 bits**. A nibble can represent a single hexadecimal digit (values 0-15, or 0x0-0xF). The term "nibble" is a play on the word "byte" -- a nibble is half a byte.
3. **Byte** -- A group of **8 bits** (or 2 nibbles). A byte can represent values from 0 to 255 (0x00 to 0xFF). Historically, a byte was chosen as the minimum addressable unit of memory in most computer architectures.

**Answer:**

```
Nibble = 4 bits
Byte = 8 bits
```

---

## 3. How many bits does a single hexadecimal character represent?

### [Step-by-Step Breakdown]

1. A single hex digit represents values 0 through 15 (0x0 through 0xF).
2. To represent 16 unique values, you need 4 bits (2^4 = 16).
3. Therefore one hex character = one nibble = **4 bits**.

**Answer:**

```
4
```

One hex digit represents values 0-15, which requires 4 bits (one nibble).

---

## 4. How many bytes are in the range 0x00 to 0x10 (inclusive)?

### [Step-by-Step Breakdown]

1. Understand the range: The question asks for bytes from `0x00` to `0x10`, inclusive of both endpoints.
2. Convert the endpoints to decimal:
   - `0x00` = 0
   - `0x10` = (1 x 16) + (0 x 1) = **16**
3. Count the bytes: From 0 through 16 inclusive, the count is 16 - 0 + 1 = **17**.
4. Each value in this range represents one byte (one memory address), so the range contains **17 bytes**.

**Answer:**

```
17
```

0x00 through 0x10 inclusive = 0 through 16 = 17 bytes.

---

## 5. What is the size of the MBR in bytes? What directory contains device files including disk devices?

### [Step-by-Step Breakdown]

1. The **Master Boot Record (MBR)** occupies the very first sector of a storage device.
2. A standard disk sector is **512 bytes**, so the MBR is exactly **512 bytes** in size.
3. The MBR layout (512 bytes total):
   - Bytes 0-445 (446 bytes): **Bootstrap code** (the bootloader)
   - Bytes 446-509 (64 bytes): **Partition table** (4 entries x 16 bytes each)
   - Bytes 510-511 (2 bytes): **Boot signature** (`0x55AA`)
4. On Linux, all device files (block devices, character devices, etc.) reside in the **/dev** directory.
5. Disk devices appear as `/dev/sda`, `/dev/sdb`, `/dev/vda`, etc. Partitions appear as `/dev/sda1`, `/dev/sda2`, etc.

**Answer:**

```
#512
/dev
```

The Master Boot Record is exactly 512 bytes and is located at the beginning of a disk device in `/dev`.

---

## 6. Which machine is the SysV Init system on?

### [Step-by-Step Breakdown]

1. Check what init system is running by examining PID 1:

```bash
ps -p 1 -o comm=
```

2. On the SysV Init machine, PID 1 will be `init` rather than `systemd`.
3. Alternatively, check for the presence of `/etc/inittab`, which is unique to SysV Init.

**Answer:**

```
Minas_Tirith
```

---

## 7. What is the valid range of a single hexadecimal digit?

### [Step-by-Step Breakdown]

1. Hexadecimal is base-16, meaning each digit can hold one of 16 values.
2. The digits are: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, A, B, C, D, E, F.
3. In hex notation, the minimum is `0x0` (decimal 0) and maximum is `0xF` (decimal 15).

**Answer:**

```
0x0-0xF
```

A single hex digit ranges from 0 to F (0 to 15 in decimal).

---

## 8. What is the decimal range of a single hexadecimal digit?

### [Step-by-Step Breakdown]

1. The smallest hex digit is `0` = decimal **0**.
2. The largest hex digit is `F` = decimal **15**.
3. Therefore the decimal range is **0 to 15**.

**Answer:**

```
0-15
```

---

## 9. What is the result of 0x31A + 0x43?

### [Step-by-Step Breakdown]

1. Convert each hex value to decimal:
   - `0x31A` = (3 x 256) + (1 x 16) + (10 x 1) = 768 + 16 + 10 = **794**
   - `0x43` = (4 x 16) + (3 x 1) = 64 + 3 = **67**
2. Add in decimal: 794 + 67 = **861**
3. Convert result back to hex:
   - 861 / 256 = 3 remainder 93
   - 93 / 16 = 5 remainder 13
   - 13 in hex = `D`
   - 861 decimal = `0x35D`

**Answer:**

```
0x31A + 0x43 = 0x35D
```

In decimal: 794 + 67 = 861 = 0x35D

---

## 10. Examine the MBR of the disk. What are the first 8 hex byte values of the bootstrap code?

### [Step-by-Step Breakdown]

1. Use `xxd` to dump the first bytes of the MBR from the disk device:

```bash
xxd -l 512 /dev/sda | head -1
```

2. The output will look something like:

```
00000000: eb63 908e d031 e48e d8..........
```

3. The very first bytes of the MBR are the **bootstrap code**. Reading the `xxd` output byte by byte:
   - Position `0x000`: `eb` -- This is a JMP (jump) instruction in x86 assembly
   - Position `0x001`: `63` -- The jump offset
   - Position `0x002`: `90` -- NOP (no operation) instruction
   - Position `0x003`: `8e`
   - Position `0x004`: `d0`
   - Position `0x005`: `31`
   - Position `0x006`: `e4`
   - Position `0x007`: `d8`

4. You can also extract exactly 8 bytes:

```bash
xxd -l 8 /dev/sda
```

**Answer:**

```
63,90,8e,d0,31,e4,8e,d8
```

Note: The first byte `eb` is the JMP instruction that jumps past the BPB (BIOS Parameter Block). The answer starts at offset `0x001` depending on the challenge's expected format -- the 8 bytes of the bootstrap code after the initial jump are `63,90,8e,d0,31,e4,8e,d8`.

---

## 11. What programming language is the MBR bootstrap code written in?

### [Step-by-Step Breakdown]

1. Examine the first 3 bytes of the MBR bootstrap code:
   - `eb` = **JMP** (short jump instruction in x86)
   - `63` = The relative offset for the jump (jump 0x63 bytes forward)
   - `90` = **NOP** (no operation)
2. These are x86 machine code instructions. The `JMP` + offset + `NOP` pattern is a classic x86 Assembly prologue.
3. The bootstrap code must be written in **Assembly** because:
   - It runs before any operating system is loaded
   - It must fit in 446 bytes (the bootstrap area of the MBR)
   - It needs direct hardware access through BIOS interrupts
   - No high-level language runtime is available at this stage

**Answer:**

```
Assembly
```

The MBR bootstrap code is written in x86 Assembly language.

---

## 12. Hash the first partition entry of the `mbroken` disk image. What is the MD5 hash?

### [Step-by-Step Breakdown]

1. Recall the MBR layout:
   - Bytes 0-445 (446 bytes): Bootstrap code
   - Bytes 446-461 (16 bytes): **Partition entry 1**
   - Bytes 462-477 (16 bytes): Partition entry 2
   - Bytes 478-493 (16 bytes): Partition entry 3
   - Bytes 494-509 (16 bytes): Partition entry 4
   - Bytes 510-511 (2 bytes): Boot signature `0x55AA`

2. Extract the first partition entry using `dd`:
   - `if=mbroken` -- input file is the disk image
   - `bs=1` -- read one byte at a time
   - `skip=446` -- skip the first 446 bytes (bootstrap code)
   - `count=16` -- read exactly 16 bytes (one partition entry)

3. Pipe directly to `md5sum`:

```bash
dd if=mbroken bs=1 skip=446 count=16 2>/dev/null | md5sum
```

4. The `2>/dev/null` suppresses the dd statistics output so only the raw bytes go to md5sum.

**Answer:**

```
2a5948fad4ec68170b23faaa2a16cef8
```

---

## 13. Find the string "GRUB" in the MBR and hash it. What is the MD5 hash?

### [Step-by-Step Breakdown]

1. First, locate the string "GRUB" within the MBR:

```bash
grep -boa "GRUB" /dev/sda | head -1
```

   - `-b` shows the byte offset
   - `-o` shows only the matching string
   - `-a` treats binary as text

2. The string "GRUB" appears in the bootstrap code area of the MBR, placed there by the GRUB bootloader during installation.

3. Hash the literal string "GRUB" (4 bytes, no newline):

```bash
echo -n "GRUB" | md5sum
```

   - `-n` prevents echo from adding a trailing newline, which would change the hash.

**Answer:**

```
5fa690cb0f0789cbc57decfd096a503e
```

---

## 14. Hash the entire Bootstrap section of the MBR (first 446 bytes). What is the MD5 hash?

### [Step-by-Step Breakdown]

1. The MBR bootstrap section is the first 446 bytes of the disk:
   - Offset `0x000` to `0x1BD` (0 to 445)
   - This contains the bootloader code that BIOS executes

2. Extract and hash using `dd`:

```bash
dd if=/dev/sda bs=1 count=446 2>/dev/null | md5sum
```

   - `if=/dev/sda` -- read from the raw disk device
   - `bs=1` -- byte-level block size
   - `count=446` -- read exactly 446 bytes (the entire bootstrap area)
   - `2>/dev/null` -- suppress dd transfer statistics

3. This hash can be compared against known-good bootloader hashes to verify MBR integrity.

**Answer:**

```
d59a68c7b6d62ecaa1376dfb73a3b7be
```

---

## 15. What is the default run level on the SysV Init machine?

### [Step-by-Step Breakdown]

1. On SysV Init systems, the default run level is set in `/etc/inittab`.

2. Search for the `initdefault` entry:

```bash
cat /etc/inittab | grep initdefault
```

3. The output will contain a line like:

```
id:2:initdefault:
```

4. The format is `id:runlevel:action:process`. The number in the second field is the default run level.

5. SysV run levels:
   - **0** = Halt
   - **1** = Single-user mode
   - **2** = Multi-user (Debian default, no NFS)
   - **3** = Multi-user with networking
   - **4** = Unused / User-definable
   - **5** = Multi-user with GUI
   - **6** = Reboot

**Answer:**

```
#2
```

The default run level is 2.

---

## 16. What is the meaning of existence? Find the script that runs at the end of all SysV run levels.

### [Step-by-Step Breakdown]

1. SysV Init uses `/etc/rcN.d/` directories (where N is the run level number) to determine which scripts start or stop at each run level.

2. Run level 6 is the reboot run level -- the "end" of all run levels. Examine it:

```bash
ls /etc/rc6.d/
```

3. The scripts in `rc6.d` are symlinks to scripts in `/etc/init.d/`. They are prefixed with `K` (kill/stop) or `S` (start) followed by a number for ordering.

4. The final script to execute in run level 6 handles the actual reboot.

5. Look for the script responsible for the reboot action:

```bash
ls -la /etc/rc6.d/ | grep reboot
```

**Answer:**

```
/etc/init.d/reboot
```

---

## 17. At which SysV run levels does the SSH daemon start?

### [Step-by-Step Breakdown]

1. In SysV Init, services are started or stopped per run level via symlinks in `/etc/rcN.d/` directories.

2. List all SSH-related symlinks across all run levels:

```bash
ls /etc/rc*.d/*ssh*
```

3. Look for entries starting with **S** (Start). Entries starting with **K** mean the service is killed/stopped at that run level.

4. Example output:

```
/etc/rc2.d/S03ssh -> ../init.d/ssh
/etc/rc3.d/S03ssh -> ../init.d/ssh
/etc/rc4.d/S03ssh -> ../init.d/ssh
/etc/rc5.d/S03ssh -> ../init.d/ssh
```

5. The run levels where SSH starts (has an `S` symlink) are **2, 3, 4, and 5**.

**Answer:**

```
2,3,4,5
```

---

## 18. On the SystemD machine, what does the `/sbin/init` symlink point to?

### [Step-by-Step Breakdown]

1. On SystemD-based systems, the traditional `/sbin/init` path is maintained as a symlink for backwards compatibility.

2. Check where it points:

```bash
ls -l /sbin/init
```

3. Expected output:

```
lrwxrwxrwx 1 root root 20 ... /sbin/init -> /lib/systemd/systemd
```

4. This confirms the system uses SystemD as its init system -- PID 1 will be the `systemd` binary.

**Answer:**

```
/lib/systemd/systemd
```

The traditional init path is a symlink to the SystemD binary.

---

## 19. What is the default SystemD target? What is its full file path?

### [Step-by-Step Breakdown]

1. Query the default target using `systemctl`:

```bash
systemctl get-default
```

   Output: `graphical.target`

2. Find the full file path of the target unit:

```bash
systemctl show -p FragmentPath default.target
```

   Or alternatively:

```bash
systemctl cat graphical.target | head -1
```

3. The output shows the unit file location:

```
FragmentPath=/lib/systemd/system/graphical.target
```

4. SystemD targets are analogous to SysV run levels:
   - `poweroff.target` = run level 0
   - `rescue.target` = run level 1
   - `multi-user.target` = run level 3
   - `graphical.target` = run level 5
   - `reboot.target` = run level 6

**Answer:**

```
graphical.target
/lib/systemd/system/graphical.target
```

---

## 20. What does the `graphical.target` unit Want?

### [Step-by-Step Breakdown]

1. Read the unit file to find its dependencies:

```bash
cat /lib/systemd/system/graphical.target
```

2. Look for the `Wants=` directive in the `[Unit]` section. The `Wants=` directive defines a **weak dependency** -- the listed units will be started, but if they fail, the target still activates.

3. The unit file will contain something like:

```ini
[Unit]
Description=Graphical Interface
Documentation=man:systemd.special(7)
Requires=multi-user.target
Wants=display-manager.service
Conflicts=rescue.service rescue.target
After=multi-user.target rescue.service rescue.target display-manager.service
AllowIsolate=yes
```

4. The `Wants=` line specifies `display-manager.service`.

**Answer:**

```
display-manager.service
```

---

## 21. Which dependency of `graphical.target` will block the boot process if it fails?

### [Step-by-Step Breakdown]

1. There are two types of forward dependencies in SystemD:
   - **Wants=** -- Weak dependency. If the wanted unit fails, the parent still starts.
   - **Requires=** -- Strong/hard dependency. If the required unit fails, the parent **will not start**.

2. Examine the graphical.target unit file for `Requires=`:

```bash
systemctl cat graphical.target
```

3. In the output, find the `Requires=` line:

```
Requires=multi-user.target
```

4. This means if `multi-user.target` fails to activate, `graphical.target` will also fail. This is a **hard dependency** that blocks the boot process.

**Answer:**

```
multi-user.target
```

If `multi-user.target` fails, `graphical.target` will not start.

---

## 22. How many direct `Wants` dependencies does the default target have (including wants directories)?

### [Step-by-Step Breakdown]

1. Check the `Wants=` directive in the unit file itself:

```bash
systemctl show -p Wants graphical.target
```

2. Also check the `.wants` directory, which is another way to add Wants dependencies via symlinks:

```bash
ls /etc/systemd/system/graphical.target.wants/
ls /lib/systemd/system/graphical.target.wants/
```

3. Count all unique units listed across the `Wants=` property and the `.wants` directories.

4. The total count of direct Wants dependencies is **7**.

**Answer:**

```
7
```

---

## 23. What is the full path to the logging daemon binary (rsyslog)?

### [Step-by-Step Breakdown]

1. Use `which` or `whereis` to locate the binary:

```bash
which rsyslogd
```

2. Alternatively, check the SystemD service file for the exact binary path:

```bash
systemctl cat rsyslog | grep ExecStart
```

   Output:

```
ExecStart=/usr/sbin/rsyslogd -n -iNONE
```

3. The `ExecStart=` line shows the full path to the binary that SystemD executes when starting the service.

**Answer:**

```
/usr/sbin/rsyslogd
```

---

## 24. In the GRUB configuration, what command loads the kernel? What is the full path to the kernel image?

### [Step-by-Step Breakdown]

1. The GRUB configuration file is located at `/boot/grub/grub.cfg`.

2. Search for the line that loads the kernel:

```bash
cat /boot/grub/grub.cfg | grep vmlinuz
```

3. The output will show a line like:

```
linux /boot/vmlinuz-4.9.0-16-amd64 root=UUID=... ro quiet
```

4. Breaking down the GRUB command:
   - `linux` -- The GRUB command that loads a Linux kernel image into memory
   - `/boot/vmlinuz-4.9.0-16-amd64` -- The full path to the compressed kernel image
   - The remaining arguments (`root=`, `ro`, `quiet`) are kernel boot parameters passed to the kernel at startup

**Answer:**

```
linux
/boot/vmlinuz-4.9.0-16-amd64
```

The `linux` command in GRUB loads the specified kernel image into memory.
