# Windows Alternate Data Streams - Challenges

Challenges covering NTFS, Master File Table, file permissions, Alternate Data Streams, and hidden files.

---

## 1. What determines how data is stored on disk?

**Answer:** Master File Table (MFT)

The MFT is the core structure of NTFS that dictates how data is organized and stored on the disk. Every file and directory on an NTFS volume has at least one entry in the MFT.

---

## 2. On an NTFS partition, sectors are grouped into what?

**Answer:** cluster or clusters

NTFS groups contiguous sectors into clusters, which serve as the fundamental unit of disk allocation. The cluster size is determined at format time and affects storage efficiency and performance.

---

## 3. What contains the metadata about every file and directory on an NTFS volume?

**Answer:** master file table (MFT)

The MFT maintains a record for every file and directory on the volume. Each record contains metadata such as file name, timestamps, security descriptors, and data location attributes.

---

## 4. On NTFS, files are a collection of what?

**Answer:** Attributes

In NTFS, everything about a file is represented as an attribute. The file name, timestamps, security information, and even the data content itself are all stored as distinct attributes within the MFT record.

---

## 5. What NTFS attribute contains the alternate data stream?

**Answer:** $DATA

The `$DATA` attribute holds the actual content of a file. A file can have multiple `$DATA` attributes -- the default (unnamed) stream and one or more named streams, which are the Alternate Data Streams (ADS).

---

## 6. What NTFS attribute contains encrypted attributes?

**Answer:** $LOGGED_UTILITY_STREAM

The `$LOGGED_UTILITY_STREAM` attribute is used by the Encrypting File System (EFS) to store encrypted attribute data. Operations on this attribute are logged in the NTFS transaction journal.

---

## 7. What NTFS attribute is used for security and access control?

**Answer:** $SECURITY_DESCRIPTOR

The `$SECURITY_DESCRIPTOR` attribute stores the security information for a file or directory, including the owner SID, group SID, DACL (Discretionary Access Control List), and SACL (System Access Control List).

---

## 8. What is the hexadecimal type ID of the $DATA attribute?

**Answer:** 0x80

Each NTFS attribute type has a numeric identifier. The `$DATA` attribute is assigned the hex type ID `0x80` (decimal 128). This identifier is used internally by NTFS when parsing MFT records.

---

## 9. What is the maximum number of bytes that can be stored in the MFT as "Resident Data"?

**Answer:** 1 KB (1024 bytes)

When file data is small enough (approximately 1024 bytes or less), NTFS can store the data directly within the MFT record itself. This is called "resident data" and avoids the overhead of allocating separate clusters on disk.

---

## 10. Permissions that are intentionally assigned to an object are called what?

**Answer:** Explicit permissions

Explicit permissions are those manually set by an administrator or user directly on a file or folder. They take precedence over inherited permissions and provide fine-grained access control.

---

## 11. Permissions that come from a parent object are called what?

**Answer:** Inherited

Inherited permissions flow down from parent containers (folders) to child objects (files and subfolders). This hierarchical model simplifies permission management across directory trees.

---

## 12. What file permission is missing? Write, Read & Execute, Modify, Full Control

**Answer:** Read

The standard NTFS file permissions are:

- Read
- Write
- Read & Execute
- Modify
- Full Control

---

## 13. What folder permission is missing? Read, Write, Read & Execute, Modify, Full Control

**Answer:** List Folder Contents

The standard NTFS folder permissions are:

- Read
- Write
- List Folder Contents
- Read & Execute
- Modify
- Full Control

The "List Folder Contents" permission is unique to folders and allows viewing the names of files and subdirectories.

---

## 14. What permission allows you to change and delete data but does not allow changing permissions?

**Answer:** Modify

The Modify permission grants the ability to read, write, execute, and delete files and subfolders, but it does not grant the ability to change permissions or take ownership of the object.

---

## 15. What folder permission allows you to change permissions?

**Answer:** Full Control

Full Control is the only standard permission level that includes the ability to change permissions and take ownership. It encompasses all other permissions plus permission management capabilities.

---

## 16. What NTFS attribute stores the file creation, modification, and access times?

**Answer:** $STANDARD_INFORMATION

The `$STANDARD_INFORMATION` attribute stores timestamps (created, modified, accessed, MFT modified), file attributes (hidden, read-only, archive, etc.), and other standard metadata for the file.

---

## 17. What CLI command will show you your drive letters? (Three words)

**Answer:** fsutil fsinfo drives

```powershell
fsutil fsinfo drives
```

This command queries the File System Utility to display all recognized drive letters on the system. It is a quick way to enumerate volumes without opening Disk Management.

---

## 18. The "d" file attribute means what?

**Answer:** Directory

When listing files with `attrib` or viewing attributes in PowerShell, the `d` attribute flag indicates that the entry is a directory (folder) rather than a regular file.

---

## 19. The "h" file attribute means what?

**Answer:** Hidden

The `h` attribute flag marks a file or directory as hidden. Hidden items are not displayed by default in Explorer or standard directory listings unless the user explicitly enables viewing hidden items.

---

## 20. What PowerShell command will list all files regardless of their attributes?

**Answer:** Get-ChildItem -Force

```powershell
Get-ChildItem -Force
```

The `-Force` parameter causes `Get-ChildItem` to display all items, including those marked as hidden or system files that would normally be excluded from directory listings.

---

## 21. What PowerShell command will give you the sha512 hash of a file?

**Answer:** Get-FileHash -Algorithm SHA512

```powershell
Get-FileHash -Algorithm SHA512 <filepath>
```

The `Get-FileHash` cmdlet computes the hash value for a file using a specified algorithm. SHA512 produces a 128-character hexadecimal hash that can be used for integrity verification.

---

## 22. What PowerShell command will list the permissions of a file?

**Answer:** Get-Acl

```powershell
Get-Acl <filepath>
```

The `Get-Acl` cmdlet retrieves the security descriptor of a file or directory, displaying the owner, group, and access control entries (ACEs) that define who can access the resource and what they can do.

---

## 23. What is the name of the Windows file that maps hostnames to IP addresses?

**Answer:** hosts

The hosts file is located at `C:\Windows\System32\drivers\etc\hosts` and provides a static, local mechanism for resolving hostnames to IP addresses before DNS queries are made.

---

## 24. What group has Read & Execute on the hosts file?

**Answer:** BuiltIN\Users

```powershell
Get-Acl C:\Windows\System32\drivers\etc\hosts | Format-List
```

The `BUILTIN\Users` group is granted Read & Execute permissions on the hosts file by default, allowing standard users to read the file but not modify it.

---

## 25. What are the last 5 characters of the MD5 hash of the hosts file?

**Steps:**

```powershell
# Step 1: Get the MD5 hash of the hosts file
Get-FileHash -Algorithm MD5 C:\Windows\System32\drivers\etc\hosts

# Step 2: Extract the last 5 characters from the resulting hash string
# The full hash will be displayed; take the final 5 characters
```

**Answer:** 7566D

---

## 26. There is a readme file somewhere in the CTF user's home directory. Read it.

**Steps:**

```powershell
# Step 1: Search recursively for any file matching "README" in the CTF user's home
Get-ChildItem -Path C:\Users\CTF -Recurse -Filter "*README*"

# Step 2: The file is found in the Favorites folder
Get-ChildItem -Path C:\Users\CTF\Favorites -Recurse -Filter "*README*"

# Step 3: Read the contents of the file
Get-Content C:\Users\CTF\Favorites\README
```

**Answer:** 123456

---

## 27. There is a hidden directory somewhere in the CTF user's home. Find the file and read it.

**Steps:**

```powershell
# Step 1: List all items in the CTF home directory, including hidden ones
Get-ChildItem -Path C:\Users\CTF -Force

# Step 2: Identify the hidden directory named "secretsauce"
Get-ChildItem -Path C:\Users\CTF\secretsauce -Force

# Step 3: Read the file named "saucey" inside the hidden directory
Get-Content C:\Users\CTF\secretsauce\saucey
```

**Answer:** ketchup

---

## 28. There is a file in a directory with spaces in its name. Find the file and read it.

**Steps:**

```powershell
# Step 1: Search through desktop directories for folders with spaces
Get-ChildItem -Path C:\Users\CTF\Desktop -Recurse -Force

# Step 2: Locate the file inside the directory with spaces in its name
# Found: spaces.txt in a directory with spaces

# Step 3: Read the content
Get-Content "C:\Users\CTF\Desktop\the directory\spaces.txt"
```

**Answer:** 987654321

---

## 29. Find the Alternate Data Stream (ADS) in the CTF user's home directory.

**Steps:**

```powershell
# Step 1: Enumerate all streams on files in the CTF home directory
Get-ChildItem -Path C:\Users\CTF -Recurse | Get-Item -Stream * 2>$null

# Step 2: Filter for files that have more than one stream (the default :$DATA plus a named ADS)
Get-ChildItem -Path C:\Users\CTF -Recurse | ForEach-Object {
    $streams = Get-Item -Path $_.FullName -Stream * 2>$null
    if ($streams.Count -gt 1) { $streams }
}

# Step 3: Identify the file "nothing_here" with a hidden ADS
Get-Item -Path C:\Users\CTF\nothing_here -Stream *

# Step 4: Read the hidden alternate data stream
Get-Content -Path C:\Users\CTF\nothing_here -Stream <hidden_stream_name>
```

**Answer:** P455W0RD

---

## 30. Fortune cookies - Find the hidden password.

**Steps:**

```powershell
# Step 1: Search the entire system for anything related to "fortune"
Get-ChildItem -Path C:\ -Recurse -Filter "*fortune*" -ErrorAction SilentlyContinue

# Step 2: Locate "The Fortune Cookie" in C:\Windows\PLA\not_anihc\
Get-ChildItem -Path "C:\Windows\PLA\not_anihc\" -Force

# Step 3: Inspect the file for Alternate Data Streams
Get-Item -Path "C:\Windows\PLA\not_anihc\The Fortune Cookie" -Stream *

# Step 4: Read the ADS named "none"
Get-Content -Path "C:\Windows\PLA\not_anihc\The Fortune Cookie" -Stream none
```

**Answer:** fortune_cookie

---

## 31. Find the phish in C:\Users\CTF.

**Steps:**

```powershell
# Step 1: Search for files with non-alphabetic characters or unusual names
Get-ChildItem -Path C:\Users\CTF -Recurse -Force

# Step 2: Look for HTML tags or web-related content
Get-ChildItem -Path C:\Users\CTF -Recurse -Force | Select-String -Pattern "<html>" -ErrorAction SilentlyContinue

# Step 3: Explore the WWW folder in the CTF user's directory
Get-ChildItem -Path C:\Users\CTF\WWW -Force -Recurse

# Step 4: Find the hidden file named "200"
Get-Content C:\Users\CTF\WWW\200
```

**Answer:** phi5hy
