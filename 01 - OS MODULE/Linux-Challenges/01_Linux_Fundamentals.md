# Linux Fundamentals - Challenges

Challenges covering Linux basics, file system, permissions, regular expressions, and text processing.

---

## 1. What command lists the contents of the current directory?

**Answer:**

```bash
ls
```

---

## 2. What command lists the contents of the current directory with detailed information and human-readable file sizes?

**Answer:**

```bash
ls -lh
```

---

## 3. What character is used to send (pipe) the output of one command as input to another command?

**Answer:**

```
|
```

The pipe character takes the standard output of the command on the left and sends it as standard input to the command on the right.

---

## 4. What command searches the short descriptions and manual page names for a keyword?

For example, searching for commands related to "copy."

**Answer:**

```bash
man -k copy
```

The `-k` flag searches all man page descriptions for the given keyword.

---

## 5. What is the top-level directory in the Linux file system, also known as the root directory?

**Answer:**

```
/
```

All files and directories stem from the root directory `/`.

---

## 6. What directory contains system-wide configuration files?

**Answer:**

```
/etc
```

The `/etc` directory holds configuration files for the system and installed services.

---

## 7. What directory contains essential user command binaries (programs) that need to be available in single-user mode?

**Answer:**

```
/bin
```

Binaries such as `ls`, `cp`, `mv`, and `cat` reside in `/bin`.

---

## 8. What directory contains most user command binaries that are not needed for single-user mode?

**Answer:**

```
usr/bin
```

The `/usr/bin` directory holds the majority of user-facing programs installed on the system.

---

## 9. What directory contains essential system administration binaries, typically only run by root?

**Answer:**

```
/sbin
```

Programs like `fdisk`, `ifconfig`, and `reboot` are located in `/sbin`.

---

## 10. Use `whereis` to locate the `cat` binary. What is the path to the man page file for `cat`?

**Answer:**

```bash
whereis cat
```

The man page path is:

```
/usr/share/man/man1/cat.1.gz
```

---

## 11. Generate the SHA-512 hash of the string "OneWayBestWay". What are the first 16 characters of the hash?

**Answer:**

```bash
echo -n "OneWayBestWay" | sha512sum
```

The hash begins with:

```
b818ef82dfdaec2e
```

---

## 12. Decrypt the file `/home/garviel/Encrypted`

The file is a zip archive protected with a password. Inside is an OpenSSL AES-128-CBC encrypted file. The decryption key is `AES128Key`.

**Steps:**

1. Unzip the archive:

```bash
unzip /home/garviel/Encrypted
```

2. Decrypt the resulting file with OpenSSL:

```bash
openssl aes-128-cbc -d -in <encrypted_file> -k AES128Key
```

**Answer:**

Follow the steps above to reveal the decrypted contents.

---

## 13. There are many files in the `/home/garviel` directory tree. Find the file with the second-most number of lines. How many lines does it have?

Use a pipeline of `find`, `file`, `grep`, `wc`, and `sort` to identify text files and count their lines.

**Answer:**

```bash
find /home/garviel -type f -exec wc -l {} + 2>/dev/null | sort -rn | head
```

The file with the second-most lines has:

```
20000
```

---

## 14. Look at the `/etc/passwd` file for something strange. What is the strange comment (GECOS field) entry?

**Answer:**

```bash
cat /etc/passwd
```

Look for an unusual entry in the GECOS (comment) field:

```
Traitor
```

---

## 15. List all members of the `lodge` group in alphabetical order, separated by commas.

**Answer:**

```bash
grep lodge /etc/group
```

Members in alphabetical order:

```
aximand,erebus,ezekyle,garviel,sejanus,tarik
```

---

## 16. Which user has a unique login shell that no other user on the system shares? What is the shell?

**Answer:**

```bash
awk -F: '{print $7}' /etc/passwd | sort | uniq -c | sort -n | head
```

The unique user and shell:

```
nobody /bin/sh
```

---

## 17. Examine the `/etc/shadow` file. What hashing algorithm is used? What is the minimum password age? How many days since epoch was the password last changed?

**Answer:**

```bash
sudo cat /etc/shadow
```

Analyze the fields:

- Hashing algorithm: **SHA-512** (indicated by `$6$` prefix)
- Minimum password age: **8**
- Days since epoch of last password change: **86**

```
SHA-512,8,86
```

---

## 18. A certain directory called `Bibliotheca` is stored somewhere on the file system. Find its full path.

**Answer:**

```bash
find / -type d -name "Bibliotheca" 2>/dev/null
```

```
/media/Bibliotheca
```

---

## 19. How many users on the system can list the contents of the `Bibliotheca` directory?

Consider users who have read and execute permissions through user, group, or other bits.

**Answer:**

```bash
ls -ld /media/Bibliotheca
```

Count all users who can access it through owner, group membership, or other permissions:

```
15
```

---

## 20. What are Sejanus's permissions on the `Bibliotheca` directory, expressed as a single octal digit?

**Answer:**

Determine whether Sejanus is the owner, in the group, or falls under "other" and read the corresponding permission bits:

```
5
```

(Read + Execute = 4 + 1 = 5)

---

## 21. Inside the Bibliotheca, find the file that user `mephiston` can modify. Mephiston is a member of the `chapter` group but NOT a member of the `lodge` group.

**Answer:**

```bash
find /media/Bibliotheca -type f -exec ls -l {} \; 2>/dev/null
```

Check group ownership and write permissions for `chapter`:

```
/media/Bibliotheca/Bibliotheca_unus/Codex_Astartes
```

---

## 22. Which file in the Bibliotheca has permissions where the group has more access than the user (owner)?

**Answer:**

```bash
find /media/Bibliotheca -type f -exec ls -l {} \;
```

Look for files where group permission bits exceed the owner bits:

```
Codex_Imperium
```

---

## 23. Execute the file named `guardsmen` as the user `gaunt`. What is the output?

**Answer:**

```bash
sudo -u gaunt ./guardsmen
```

```
GHOSTS
```

---

## 24. The user `tyborc` cannot access a certain file. What specific permission is tyborc missing?

**Answer:**

Tyborc is missing the permission needed to traverse the directory:

```
execute
```

Without execute permission on a directory, a user cannot access files within it.

---

## 25. Quixos is the sole user who can modify a certain file in the Bibliotheca. What is the full path to that file?

**Answer:**

```bash
find /media/Bibliotheca -type f -user quixos -perm -u=w 2>/dev/null
```

```
/media/Bibliotheca/Bibliotheca_duo/Codex_Hereticus
```

---

## 26. There is a concealed (hidden) file somewhere in the Bibliotheca. What is its contents?

**Answer:**

```bash
find /media/Bibliotheca -name ".*" -type f 2>/dev/null
cat <hidden_file>
```

```
Expand your mind
```

---

## 27. Locate the hidden warp secrets file and read its contents. What does it say?

**Answer:**

```bash
find /media/Bibliotheca -name "*warp*" -o -name "*secret*" 2>/dev/null
```

```
Ph'nglui mglw'nafh Cthulhu
```

---

## 28. How many directories in `/etc/` end with `.d`?

**Answer:**

```bash
find /etc/ -maxdepth 1 -type d -name "*.d" | wc -l
```

```
28
```

---

## 29. Write a regular expression that matches a valid IPv4 address in the range 0-255 per octet. Use it to extract valid IPs from the provided data set.

**Answer:**

A basic regex pattern for valid IPv4 addresses:

```bash
grep -P '\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\b' <file>
```

Use this pattern to filter out invalid addresses from the data.

---

## 30. From the same data set, identify all INVALID IP addresses (those with octets outside the 0-255 range or malformed entries).

**Answer:**

Use the inverse match or a complementary regex to find lines that do not match the valid IP pattern:

```bash
grep -vP '\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\b' <file>
```

Compare results with the valid set to confirm all entries are accounted for.

---

## 31. Using regular expressions, count the number of valid MAC addresses in the provided data file. MAC addresses use the format `XX:XX:XX:XX:XX:XX` where X is a hex digit.

**Answer:**

```bash
grep -cP '\b([0-9A-Fa-f]{2}:){5}[0-9A-Fa-f]{2}\b' <file>
```

```
4877
```

---

## 32. Use `awk` to extract lines 420 through 1337 from the provided file, then generate a SHA-512 hash of the output. What are the first 8 characters of the hash?

**Answer:**

```bash
awk 'NR>=420 && NR<=1337' <file> | sha512sum
```

The hash begins with:

```
e62ff70d
```

---

## 33. Use `awk` to parse the `connections` file and produce a CSV-formatted output. Then generate an MD5 hash of the result.

**Answer:**

```bash
awk '{print $1","$2","$3}' connections > output.csv
md5sum output.csv
```

```
6cebf155
```

---

## 34. Navigate the Titan minefield. Follow the instructions in the challenge to traverse the correct path through the directories.

**Answer:**

Follow each step carefully, reading clues in each directory to determine the next move. The final MD5 flag is:

```
fac096f90e22d4c678e0a68fe0b17c7b
```

---

## 35. Match the flag to the correct binary in the system PATH. Which binary does the flag belong to, and what is the flag?

**Answer:**

Search through binaries in the system PATH to locate the embedded flag:

```bash
which python3
strings $(which python3) | grep FLAG
```

```
python3, FLAG{Vrc0vw7ZUaLBpQp}
```

---

## 36. From the MAC address data set, identify which addresses are LAA (Locally Administered Addresses) vs UAA (Universally Administered Addresses). How many total LAA and UAA addresses are there combined?

The second-least-significant bit of the first octet determines LAA (bit set to 1) vs UAA (bit set to 0).

**Answer:**

```bash
grep -P '\b[0-9A-Fa-f][2367AaBbEeFf]:[0-9A-Fa-f]{2}:' <file> | wc -l
```

Count both LAA and UAA:

```
178
```

---

## 37. Identify the Heretics: given a list of guardsmen and a list of known targets, determine how many guardsmen are actually heretics (appear on both lists).

**Answer:**

```bash
comm -12 <(sort guardsmen.txt) <(sort targets.txt) | wc -l
```

```
8
```
