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

1. First, identify the file type to understand what we are working with:

```bash
file /home/garviel/Encrypted
```

This reveals the file is a **zip archive**.

2. Attempt to unzip the archive:

```bash
unzip /home/garviel/Encrypted
```

The archive is **password-protected**, so it will prompt for a password.

3. Unzip with the password. Try common passwords or hints provided in the challenge. The zip password may be found through other challenge clues or brute-force:

```bash
unzip -P <zip_password> /home/garviel/Encrypted
```

This extracts the encrypted file inside the archive.

4. Identify the extracted file type:

```bash
file <extracted_file>
```

This shows the extracted file is an **OpenSSL encrypted** data file (AES-128-CBC).

5. Decrypt the file using OpenSSL with the provided key `AES128Key`:

```bash
openssl aes-128-cbc -d -in <extracted_file> -k AES128Key
```

If using a newer version of OpenSSL, you may need to add the `-md md5` flag for compatibility with older encryption formats:

```bash
openssl aes-128-cbc -d -in <extracted_file> -k AES128Key -md md5
```

6. The decrypted output displays the **flag** or answer text. Read the output carefully -- this is the answer to the challenge.

**Flag:** The decrypted contents of the file.

---

## 13. There are many files in the `/home/garviel` directory tree. Find the file with the second-most number of lines. How many lines does it have?

This requires building a pipeline step by step. Each step builds on the previous one.

**Step-by-step pipeline breakdown:**

1. **Find all regular files** in the `/home/garviel` directory tree:

```bash
find /home/garviel -type f
```

This lists every file path recursively under `/home/garviel`.

2. **Filter for only text files** using `file` and `grep` (to avoid counting lines in binary files):

```bash
find /home/garviel -type f -exec file {} \; | grep -i text
```

This runs `file` on each result and filters to show only text files. The output shows each file path followed by its type description.

3. **Extract just the file paths** from the `file` command output using `cut`:

```bash
find /home/garviel -type f -exec file {} \; | grep -i text | cut -d: -f1
```

The `cut -d: -f1` splits each line on the colon and keeps only the file path (field 1).

4. **Count the lines in each text file** by piping the file paths into a `while read` loop with `wc -l`:

```bash
find /home/garviel -type f -exec file {} \; | grep -i text | cut -d: -f1 | xargs wc -l
```

Alternatively using `wc -l` directly with `find -exec`:

```bash
find /home/garviel -type f -exec wc -l {} + 2>/dev/null
```

This outputs each file with its line count.

5. **Sort the results numerically in reverse order** (largest first):

```bash
find /home/garviel -type f -exec wc -l {} + 2>/dev/null | sort -rn
```

The `-rn` flags sort in reverse (`-r`) numerical (`-n`) order, putting the highest line counts at the top.

6. **Display only the top results** to find the second-most:

```bash
find /home/garviel -type f -exec wc -l {} + 2>/dev/null | sort -rn | head
```

The first entry (excluding "total" lines) is the file with the most lines. The second entry is the file with the second-most lines.

7. **Final combined command:**

```bash
find /home/garviel -type f -exec wc -l {} + 2>/dev/null | sort -rn | head
```

Look at the output -- the first result is the file with the most lines, and the second result is the file with the second-most number of lines.

**Answer:**

```
20000
```

---

## 14. Look at the `/etc/passwd` file for something strange. What is the strange comment (GECOS field) entry?

The `/etc/passwd` file has 7 colon-delimited fields per line:
`username:password:UID:GID:GECOS:home_directory:shell`

The GECOS field (field 5) normally contains the user's full name or description.

**Step-by-step process:**

1. **View the entire passwd file** to get an overview:

```bash
cat /etc/passwd
```

This displays all user entries. Look through the output for anything unusual.

2. **Extract just the GECOS (comment) field** to make unusual entries easier to spot:

```bash
awk -F: '{print $5}' /etc/passwd
```

The `-F:` sets the field separator to colon, and `$5` prints the 5th field (GECOS).

3. **Print both the username and GECOS field** for context:

```bash
awk -F: '{print $1, $5}' /etc/passwd
```

This shows each username alongside its comment field, making it easier to spot something out of place.

4. **Sort or filter to find the anomaly.** Most GECOS fields will be empty or contain standard descriptions. Look for one that stands out:

```bash
awk -F: '$5 != "" {print $1, $5}' /etc/passwd
```

This filters to show only entries where the GECOS field is not empty.

5. Review the output -- one entry has an unusual, non-standard comment that does not belong. This is the "strange" entry.

**Answer:**

```
Traitor
```

---

## 15. List all members of the `lodge` group in alphabetical order, separated by commas.

**Step-by-step pipeline:**

1. **Find the lodge group entry** in `/etc/group`:

```bash
grep lodge /etc/group
```

The output shows the group entry in the format: `group_name:password:GID:member_list`. The member list is comma-separated.

2. **Extract just the member list** (field 4) using `cut`:

```bash
grep lodge /etc/group | cut -d: -f4
```

This gives the raw comma-separated list of members.

3. **Convert commas to newlines** so each member is on its own line (for sorting):

```bash
grep lodge /etc/group | cut -d: -f4 | tr ',' '\n'
```

The `tr ',' '\n'` translates each comma into a newline character.

4. **Sort the members alphabetically:**

```bash
grep lodge /etc/group | cut -d: -f4 | tr ',' '\n' | sort
```

This puts all members in alphabetical order, one per line.

5. **Rejoin into a comma-separated list** using `paste` or `tr`:

```bash
grep lodge /etc/group | cut -d: -f4 | tr ',' '\n' | sort | paste -sd,
```

The `paste -sd,` joins all lines into a single line with commas as separators.

**Combined command:**

```bash
grep lodge /etc/group | cut -d: -f4 | tr ',' '\n' | sort | paste -sd,
```

**Answer:**

```
aximand,erebus,ezekyle,garviel,sejanus,tarik
```

---

## 16. Which user has a unique login shell that no other user on the system shares? What is the shell?

**Answer:**

```bash
awk -F: '{print $7}' /etc/passwd | sort | uniq -c | sort -n | head
```

Look for a shell that appears only once (count of 1). Then find which user has that shell:

```bash
awk -F: '$7 == "/bin/sh" {print $1}' /etc/passwd
```

The unique user and shell:

```
nobody /bin/sh
```

---

## 17. Examine the `/etc/shadow` file. What hashing algorithm is used? What is the minimum password age? How many days since epoch was the password last changed?

The `/etc/shadow` file stores password hashes and aging information. Each line has 9 colon-delimited fields:
`username:password_hash:last_changed:min_age:max_age:warn:inactive:expire:reserved`

**Step-by-step analysis:**

1. **Read the shadow file** (requires elevated privileges):

```bash
sudo cat /etc/shadow
```

Look at the password hash field (field 2) for a real user account (not system accounts with `*` or `!`).

2. **Identify the hashing algorithm** from the hash prefix. The password hash field uses the format `$id$salt$hash`:
   - `$1$` = MD5
   - `$5$` = SHA-256
   - `$6$` = SHA-512

The hash begins with **`$6$`**, which indicates **SHA-512**.

3. **Examine the salt** -- the characters between the second and third `$` signs. The salt is a random string used to make identical passwords produce different hashes. Note the salt characters for reference.

4. **Check the hash length** -- SHA-512 produces a significantly longer hash string compared to MD5 or SHA-256. This confirms the `$6$` identification.

5. **Read the aging fields** from the shadow entry:
   - **Field 3** (last changed): The number of days since the Unix epoch (January 1, 1970) when the password was last changed. This value is **86**.
   - **Field 4** (minimum age): The minimum number of days that must pass before the user can change their password again. This value is **8**.

**Answer:**

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

1. Check the directory permissions and ownership:

```bash
ls -ld /media/Bibliotheca
```

This shows the permission bits, owner, and group for the directory. To list a directory's contents, a user needs both **read (r)** and **execute (x)** permissions.

2. Determine who has access:
   - The **owner** -- check if the owner bits include `r` and `x`
   - **Group members** -- check if the group bits include `r` and `x`, then count members of that group in `/etc/group`
   - **Others** -- check if the other bits include `r` and `x`, then count all remaining users

3. Add up all users who can access it through owner, group membership, or other permissions.

**Answer:**

```
15
```

---

## 20. What are Sejanus's permissions on the `Bibliotheca` directory, expressed as a single octal digit?

**Answer:**

1. Check the directory's permissions, owner, and group:

```bash
ls -ld /media/Bibliotheca
```

2. Determine which permission category Sejanus falls into:
   - Is Sejanus the **owner** of the directory?
   - Is Sejanus a member of the directory's **group**?
   - If neither, Sejanus falls under **other**

3. Read the corresponding permission bits and convert to octal:

```
5
```

(Read + Execute = 4 + 1 = 5)

---

## 21. Inside the Bibliotheca, find the file that user `mephiston` can modify. Mephiston is a member of the `chapter` group but NOT a member of the `lodge` group.

**Step-by-step process:**

1. **List all files in the Bibliotheca** with their full permissions and ownership:

```bash
find /media/Bibliotheca -type f -exec ls -l {} \; 2>/dev/null
```

This shows the permission string, owner, group, size, and path for every file. Examine each line carefully.

2. **Determine mephiston's access for each file.** For each file, check:
   - Is mephiston the **owner**? If so, look at the user (owner) permission bits.
   - Is the file's group one that mephiston belongs to? Mephiston is in `chapter` but NOT in `lodge`. If the file's group is `chapter`, look at the group permission bits.
   - Otherwise, mephiston falls under **other** permission bits.

3. **Look for write (w) permission** in the applicable category. A file that mephiston can "modify" means write permission exists for mephiston's applicable category.

4. **Verify group membership:**

```bash
grep chapter /etc/group
```

Confirm mephiston is listed as a member of `chapter`.

```bash
grep lodge /etc/group
```

Confirm mephiston is NOT listed as a member of `lodge`.

5. The file where the `chapter` group has write permission (and mephiston is not the owner, so group bits apply):

**Answer:**

```
/media/Bibliotheca/Bibliotheca_unus/Codex_Astartes
```

---

## 22. Which file in the Bibliotheca has permissions where the group has more access than the user (owner)?

**Step-by-step process:**

1. **List all files with permissions:**

```bash
find /media/Bibliotheca -type f -exec ls -l {} \; 2>/dev/null
```

2. **For each file, compare the user (owner) bits to the group bits.** The permission string format is:
   `-rwxrwxrwx` where positions 1-3 are user, 4-6 are group, 7-9 are other.

3. **Convert each set to octal** and compare. Look for a file where the group's octal value is higher than the owner's octal value.

4. **Identify the anomalous file** -- normally the owner has equal or greater permissions than the group. The file where this is reversed is the answer.

**Answer:**

```
Codex_Imperium
```

---

## 23. Execute the file named `guardsmen` as the user `gaunt`. What is the output?

**Step-by-step process:**

1. **Find the `guardsmen` file:**

```bash
find /media/Bibliotheca -name "guardsmen" 2>/dev/null
```

2. **Check the file's permissions** to see who can execute it:

```bash
ls -l <path_to_guardsmen>
```

3. **Execute the file as user `gaunt`** using `sudo`:

```bash
sudo -u gaunt <path_to_guardsmen>
```

4. Read the output -- this is the answer.

**Answer:**

```
GHOSTS
```

---

## 24. The user `tyborc` cannot access a certain file. What specific permission is tyborc missing?

**Step-by-step process:**

1. **Identify which file tyborc cannot access** by checking all files in the Bibliotheca:

```bash
find /media/Bibliotheca -type f -exec ls -l {} \; 2>/dev/null
```

2. **Determine tyborc's group memberships:**

```bash
groups tyborc
```

3. **For each file, determine tyborc's effective permissions** based on whether tyborc is the owner, in the group, or falls under other.

4. **Identify the permission gap.** Tyborc can see the file exists but cannot access it. To access a file inside a directory, the user needs **execute** permission on the directory itself.

5. Without **execute** permission on a directory, a user cannot traverse into it to access files within, even if the file itself has open permissions.

**Answer:**

```
execute
```

Without execute permission on a directory, a user cannot access files within it.

---

## 25. Quixos is the sole user who can modify a certain file in the Bibliotheca. What is the full path to that file?

**Step-by-step process:**

1. **List all files with permissions and ownership:**

```bash
find /media/Bibliotheca -type f -exec ls -l {} \; 2>/dev/null
```

2. **Find files owned by quixos** where only the owner has write permission:

```bash
find /media/Bibliotheca -type f -user quixos -perm -u=w 2>/dev/null
```

3. **Verify that no group or other write bits are set** -- only the user (owner) write bit should be set, making quixos the sole user who can modify the file.

4. **Confirm quixos's ownership:**

```bash
ls -l /media/Bibliotheca/Bibliotheca_duo/Codex_Hereticus
```

The output shows quixos as the owner with write permission, and no group or other write permission.

**Answer:**

```
/media/Bibliotheca/Bibliotheca_duo/Codex_Hereticus
```

---

## 26. There is a concealed (hidden) file somewhere in the Bibliotheca. What is its contents?

**Step-by-step process:**

1. **Search for hidden files** (files starting with a dot) in the Bibliotheca:

```bash
find /media/Bibliotheca -name ".*" -type f 2>/dev/null
```

Hidden files in Linux are any files whose name begins with a `.` character. They do not show up with a regular `ls` command -- you need `ls -a` to see them.

2. **Read the hidden file's contents:**

```bash
cat <path_to_hidden_file>
```

3. Alternatively, search recursively and display contents in one step:

```bash
find /media/Bibliotheca -name ".*" -type f -exec cat {} \; 2>/dev/null
```

**Answer:**

```
Expand your mind
```

---

## 27. Locate the hidden warp secrets file and read its contents. What does it say?

**Step-by-step process:**

1. **Search for files with "warp" or "secret" in the name:**

```bash
find /media/Bibliotheca -name "*warp*" -o -name "*secret*" 2>/dev/null
```

2. **Check for hidden directories** that may contain additional files:

```bash
find /media/Bibliotheca -type d -name ".*" 2>/dev/null
```

3. **Use `sudo` if needed** to access restricted directories or files:

```bash
sudo find /media/Bibliotheca -name "*warp*" -o -name "*secret*" 2>/dev/null
```

4. **Read the contents** of the located file:

```bash
sudo cat <path_to_warp_secrets_file>
```

**Answer:**

```
Ph'nglui mglw'nafh Cthulhu
```

---

## 28. How many directories in `/etc/` end with `.d`?

**Step-by-step process:**

1. **Find directories at the top level of `/etc/`** that end with `.d`:

```bash
find /etc/ -maxdepth 1 -type d -name "*.d"
```

This lists all matching directories. The `-maxdepth 1` limits the search to the immediate contents of `/etc/` (not nested subdirectories).

2. **Count the results** by piping to `wc -l`:

```bash
find /etc/ -maxdepth 1 -type d -name "*.d" | wc -l
```

3. **Verify** by listing them to confirm they are actual `.d` directories:

```bash
ls -d /etc/*.d
```

**Answer:**

```
28
```

---

## 29. Write a regular expression that matches a valid IPv4 address in the range 0-255 per octet. Use it to extract valid IPs from the provided data set.

**Step-by-step process:**

1. **Understand IPv4 format:** Four octets separated by dots, each octet ranging from 0 to 255. For example: `192.168.1.1`.

2. **Build the regex for a single octet (0-255):**
   - `25[0-5]` matches 250-255
   - `2[0-4][0-9]` matches 200-249
   - `[01]?[0-9][0-9]?` matches 0-199
   - Combined: `(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)`

3. **Combine for the full IPv4 address** (4 octets with dots between them):

```
\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\b
```

4. **Apply with grep** to extract valid IPs from the data file:

```bash
grep -P '\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\b' <file>
```

5. **Count the valid IPs** if needed:

```bash
grep -cP '\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\b' <file>
```

Use this pattern to filter out invalid addresses from the data.

---

## 30. From the same data set, identify all INVALID IP addresses (those with octets outside the 0-255 range or malformed entries).

**Step-by-step process:**

1. **Use the inverse match (`-v`)** with the valid IP regex to find lines that do NOT contain a valid IPv4 address:

```bash
grep -vP '\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\b' <file>
```

The `-v` flag inverts the match, showing only lines that fail the valid IP pattern.

2. **Review the invalid entries** -- they may include:
   - Octets greater than 255 (e.g., `192.168.300.1`)
   - Too few or too many octets (e.g., `10.0.1` or `10.0.0.1.5`)
   - Non-numeric characters in octets
   - Leading zeros that indicate octal notation

3. **Count the invalid entries:**

```bash
grep -cvP '\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\b' <file>
```

Compare results with the valid set to confirm all entries are accounted for.

---

## 31. Using regular expressions, count the number of valid MAC addresses in the provided data file. MAC addresses use the format `XX:XX:XX:XX:XX:XX` where X is a hex digit.

**Step-by-step process:**

1. **Understand MAC address format:** Six groups of two hexadecimal digits, separated by colons. Hex digits are `0-9`, `A-F`, and `a-f`.

2. **Build the regex:**

```
\b([0-9A-Fa-f]{2}:){5}[0-9A-Fa-f]{2}\b
```

This matches exactly 6 pairs of hex digits separated by colons.

3. **Count matching lines:**

```bash
grep -cP '\b([0-9A-Fa-f]{2}:){5}[0-9A-Fa-f]{2}\b' <file>
```

**Answer:**

```
4877
```

---

## 32. Use `awk` to extract lines 420 through 1337 from the provided file, then generate a SHA-512 hash of the output. What are the first 8 characters of the hash?

**Step-by-step process:**

1. **Extract the line range using awk.** The `NR` variable in awk tracks the current line number:

```bash
awk 'NR>=420 && NR<=1337' <file>
```

This prints only lines 420 through 1337 (inclusive).

2. **Verify the extraction** by counting the output lines (should be 1337 - 420 + 1 = 918 lines):

```bash
awk 'NR>=420 && NR<=1337' <file> | wc -l
```

3. **Pipe the extracted lines into `sha512sum`** to generate the hash:

```bash
awk 'NR>=420 && NR<=1337' <file> | sha512sum
```

4. **Read the first 8 characters** of the resulting hash string.

**Answer:**

```
e62ff70d
```

---

## 33. Use `awk` to parse the `connections` file and produce a CSV-formatted output. Then generate an MD5 hash of the result.

**Step-by-step process:**

1. **Examine the `connections` file** to understand its structure:

```bash
head connections
```

The file likely has whitespace-delimited fields.

2. **Use awk to convert to CSV.** By default, awk splits fields on whitespace. Print them with commas:

```bash
awk '{print $1","$2","$3}' connections
```

Adjust the number of fields as needed based on the file's actual structure.

3. **Save the CSV output to a file:**

```bash
awk '{print $1","$2","$3}' connections > output.csv
```

4. **Generate the MD5 hash:**

```bash
md5sum output.csv
```

Alternatively, combine into one pipeline without creating a file:

```bash
awk '{print $1","$2","$3}' connections | md5sum
```

5. **Read the first 8 characters** of the MD5 hash.

**Answer:**

```
6cebf155
```

---

## 34. Navigate the Titan minefield. Follow the instructions in the challenge to traverse the correct path through the directories.

**Step-by-step process:**

1. **Locate the starting point** of the Titan minefield challenge.

2. **Read the instructions** in each directory (often in README or hint files) to determine which subdirectory to enter next.

3. **Navigate step by step,** reading clues at each stage. Use `ls -la` to see hidden files and `cat` to read any instruction files.

4. **Follow the correct path** through each level of directories. Wrong turns may contain "mine" indicators or dead ends.

5. **At the final destination,** retrieve the flag or generate the MD5 hash as instructed.

**Answer:**

The final MD5 flag is:

```
fac096f90e22d4c678e0a68fe0b17c7b
```

---

## 35. Match the flag to the correct binary in the system PATH. Which binary does the flag belong to, and what is the flag?

**Step-by-step process:**

1. **List all directories in the system PATH:**

```bash
echo $PATH | tr ':' '\n'
```

2. **Search for binaries containing a flag string.** Use `strings` to extract readable text from binaries and `grep` to find the flag:

```bash
which python3
strings $(which python3) | grep FLAG
```

3. **Try different binaries** if the first attempt does not match. Check common binaries or use a broader search:

```bash
for bin in $(ls /usr/bin); do strings /usr/bin/$bin 2>/dev/null | grep -l FLAG && echo $bin; done
```

4. The flag is embedded inside the `python3` binary.

**Answer:**

```
python3, FLAG{Vrc0vw7ZUaLBpQp}
```

---

## 36. From the MAC address data set, identify which addresses are LAA (Locally Administered Addresses) vs UAA (Universally Administered Addresses). How many total LAA and UAA addresses are there combined?

The second-least-significant bit of the first octet determines LAA (bit set to 1) vs UAA (bit set to 0).

**Step-by-step process:**

1. **Understand the LAA/UAA bit.** In the first octet of a MAC address, the second-least-significant bit (bit 1) indicates:
   - **0** = UAA (Universally Administered Address) -- assigned by the manufacturer
   - **1** = LAA (Locally Administered Address) -- assigned by the network administrator

2. **Determine which hex digits have bit 1 set.** The second hex character of the first octet determines this:
   - LAA second hex chars: `2, 3, 6, 7, A, B, E, F` (and lowercase equivalents)
   - UAA second hex chars: `0, 1, 4, 5, 8, 9, C, D` (and lowercase equivalents)

3. **Count LAA addresses** using grep with a character class for the second hex digit:

```bash
grep -cP '\b[0-9A-Fa-f][2367AaBbEeFf]:' <file>
```

4. **Count UAA addresses** similarly:

```bash
grep -cP '\b[0-9A-Fa-f][014589CcDd]:' <file>
```

5. **Add the counts** together for the total.

**Answer:**

```
178
```

---

## 37. Identify the Heretics: given a list of guardsmen and a list of known targets, determine how many guardsmen are actually heretics (appear on both lists).

**Step-by-step process:**

1. **Examine both files** to understand the format:

```bash
head guardsmen.txt
head targets.txt
```

Each file contains a list of names, one per line.

2. **Sort both files** (required for comparison tools like `comm`):

```bash
sort guardsmen.txt > /tmp/sorted_guardsmen.txt
sort targets.txt > /tmp/sorted_targets.txt
```

3. **Find names that appear in BOTH files** using `comm -12`:

```bash
comm -12 <(sort guardsmen.txt) <(sort targets.txt)
```

The `comm` command compares two sorted files. The `-12` flag suppresses columns 1 and 2 (lines unique to each file), showing only column 3 (lines common to both files).

4. **Review the heretics** -- these are the guardsmen who also appear on the targets list:

```bash
comm -12 <(sort guardsmen.txt) <(sort targets.txt)
```

5. **Count the heretics:**

```bash
comm -12 <(sort guardsmen.txt) <(sort targets.txt) | wc -l
```

6. **One-liner combining everything:**

```bash
comm -12 <(sort guardsmen.txt) <(sort targets.txt) | wc -l
```

This sorts both files on the fly using process substitution `<(...)`, finds common lines with `comm -12`, and counts them with `wc -l`.

**Answer:**

```
8
```
