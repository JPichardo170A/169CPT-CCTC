# Windows Active Directory Enumeration - Challenges

Challenges covering Active Directory enumeration using PowerShell cmdlets.

---

## 1. What database connects users with the resources they need to get their work done?

**Answer:** `Active Directory`

---

## 2. Everything in Active Directory is stored as what?

**Answer:** `object`

---

## 3. What component of Active Directory contains the formal definitions of every object class that can be created and stored?

**Answer:** `Active Directory Schema`

---

## 4. What protocol is used for accessing and maintaining distributed directory information services for Active Directory?

**Answer:** `LDAP`

---

## 5. From an offensive perspective, what is the main target account type?

**Answer:** `Administrator`

---

## 6. What should be done with inactive accounts?

**Answer:** `Disable`

---

## 7. What is the basic PowerShell cmdlet used for user enumeration?

**Answer:** `Get-ADUser`

---

## 8. What suite of CLI tools is used for network enumeration in Active Directory?

**Answer:** `Domain Services`

---

## 9. What is the domain portion of the SID S-1-5-21-1004336348-1177238915-682003330-1000?

The SID structure is: `S-<revision>-<authority>-<domain>-<RID>`

- **Revision:** 1
- **Authority:** 5
- **Domain:** 21-1004336348-1177238915-682003330
- **RID:** 1000

**Answer:** `21-1004336348-1177238915-682003330`

---

## 10. What PowerShell command lists all domain groups?

```powershell
Get-ADGroup -Filter *
```

**Answer:** `Get-ADGroup -Filter *`

---

## 11. What PowerShell command lists all users with their properties?

```powershell
Get-ADUser -Filter 'Name -like "*"'
```

**Answer:** `Get-ADUser -Filter 'Name -like "*"'`

---

## 12. What PowerShell cmdlet is used to find expired accounts?

**Answer:** `search-ADAccount`

---

## 13. What are the last names of accounts that are expired but not disabled, in alphabetical order?

We need to find accounts that have expired but are still enabled. This requires a progressive troubleshooting approach to build the correct query.

### Attempt 1: Find expired accounts

Start by using `Search-ADAccount` to identify all expired accounts:

```powershell
Search-ADAccount -AccountExpired
```

This returns a list of all accounts whose expiration date has passed. However, this includes both enabled and disabled accounts -- we need to filter further.

### Attempt 2: Filter for only enabled (not disabled) accounts

Pipe the results into `Where-Object` to keep only accounts that are still enabled:

```powershell
Search-ADAccount -AccountExpired | Where-Object {$_.Enabled -eq $true}
```

This narrows the results to accounts that are expired but NOT disabled. Now we need to extract and sort their last names.

### Attempt 3: Get the Surname property and sort

Pipe the filtered accounts into `Get-ADUser` to retrieve the `AccountExpirationDate` and `Surname` properties, then sort alphabetically:

```powershell
Search-ADAccount -AccountExpired | Where-Object {$_.Enabled -eq $true} | Get-ADUser -Properties AccountExpirationDate, Surname | Sort-Object Surname | Select-Object Surname
```

Output:

```
Surname
-------
Krause
Page
```

### Final combined command

To produce the comma-separated answer in one shot:

```powershell
(Search-ADAccount -AccountExpired | Where-Object {$_.Enabled -eq $true} | Get-ADUser -Properties AccountExpirationDate, Surname | Sort-Object Surname | Select-Object -ExpandProperty Surname) -join ','
```

Output:

```
Krause,Page
```

**Answer:** `Krause,Page`

---

## 14. What unprofessional email domains are found in Active Directory user accounts?

We need to enumerate all user email addresses and identify any domains that are not the organization's official `.mil` domain.

### Step 1: Query all users for their Mail property

```powershell
Get-ADUser -Filter * -Properties Mail | Where-Object {$_.Mail -ne $null} | Select-Object Name, Mail
```

This returns all AD users that have an email address set. Review the output for any addresses that do not use the official `.mil` domain.

### Step 2: Filter out the legitimate .mil addresses

```powershell
Get-ADUser -Filter * -Properties Mail | Where-Object {$_.Mail -ne $null -and $_.Mail -notlike "*.mil"} | Select-Object Name, Mail
```

This filters to only show users whose email is NOT a `.mil` address, revealing any unprofessional or unauthorized email domains.

### Step 3: Extract just the domain portion using Split('@')

```powershell
Get-ADUser -Filter * -Properties Mail | Where-Object {$_.Mail -ne $null -and $_.Mail -notlike "*.mil"} | ForEach-Object { $_.Mail.Split('@')[1] }
```

This splits each email address at the `@` symbol and returns only the domain portion.

### Step 4: Get unique unprofessional domains sorted

```powershell
Get-ADUser -Filter * -Properties Mail | Where-Object {$_.Mail -ne $null -and $_.Mail -notlike "*.mil"} | ForEach-Object { $_.Mail.Split('@')[1] } | Sort-Object -Unique
```

Output:

```
ashleymadison.com
```

**Answer:** `ashleymadison.com`

---

## 15. What unprofessional file is found on the Warrior Share?

**Answer:** `lulz.pdf`

---

## 16. What file on the Warrior Share has modified access rights in the request?

We need to map the Warrior Share, list its contents, and search through files for keywords related to access modification requests.

### Step 1: Map the Warrior Share

```powershell
net use * "\\file-server\Warrior Share"
```

This maps the Warrior Share to the next available drive letter (e.g., `Z:`).

### Step 2: List all files on the share

```powershell
dir "\\file-server\Warrior Share" /s
```

Or if mapped to a drive letter:

```powershell
Get-ChildItem Z:\ -Recurse
```

This lists all files and subdirectories on the Warrior Share, showing the various PDFs and documents stored there.

### Step 3: Search file contents for access rights / modification keywords

```powershell
Select-String -Path "Z:\*.pdf" -Pattern "access rights|modify|permissions|request" -SimpleMatch
```

Or more broadly using a keyword search across text-readable files:

```powershell
Get-ChildItem Z:\ -Recurse -Include *.txt,*.csv,*.log | Select-String -Pattern "access rights|modify|permissions|request"
```

For PDF files, open each and search for keywords related to modifying access rights. The file `14287.pdf` contains a request to modify access rights.

### Step 4: Identify the file

After reviewing the files on the share, file `14287.pdf` contains a request with modified access rights.

**Answer:** `14287.pdf`

---

## 17. What user is requesting modified access in that file?

### Step 1: Extract the phone number from the PDF

Open `14287.pdf` from the Warrior Share and review its contents. The document is an access request form that contains a phone number: `336-6754`.

### Step 2: Search Active Directory for the user with that phone number

Use `Get-ADUser` with the `OfficePhone` property to find who owns that number:

```powershell
Get-ADUser -Filter * -Properties OfficePhone | Where-Object {$_.OfficePhone -like "*336-6754*"} | Select-Object Name, SamAccountName, OfficePhone
```

Output:

```
Name         SamAccountName OfficePhone
----         -------------- -----------
Karen Nance  Karen.Nance    336-6754
```

The phone number `336-6754` from the PDF belongs to Karen.Nance.

**Answer:** `Karen.Nance`

---

## 18. What accounts have unprofessional descriptions? (alphabetical order, comma-separated)

### Step 1: Import the Active Directory module

```powershell
Import-Module ActiveDirectory
```

### Step 2: Query all users and retrieve their Description field

```powershell
Get-ADUser -Filter * -Properties Description | Where-Object {$_.Description -ne $null -and $_.Description -ne ""} | Sort-Object Name | Select-Object Name, Description
```

### Step 3: Identify accounts with unprofessional descriptions

Review the output for any descriptions that are inappropriate, unprofessional, or do not belong in an enterprise AD environment. The following accounts are flagged:

```
Name        Description
----        -----------
Brandywine  <unprofessional description>
Ibarra      <unprofessional description>
Jimenez     <unprofessional description>
Lee         <unprofessional description>
```

These four accounts contain descriptions that are inappropriate for a military/professional AD environment.

**Answer:** `Brandywine,Ibarra,Jimenez,Lee`

---

## 19. What accounts have passwords set to never expire AND store passwords using reversible encryption? (alphabetical order, comma-separated)

### Step 1: Query for users with PasswordNeverExpires set to true

```powershell
Get-ADUser -Filter {PasswordNeverExpires -eq $true} -Properties PasswordNeverExpires, UserAccountControl | Select-Object Name, PasswordNeverExpires, UserAccountControl
```

### Step 2: Filter for reversible encryption using UserAccountControl

The `UserAccountControl` attribute is a bitmask. The flag for "Store password using reversible encryption" is `0x80` (128). The flag for "Password never expires" is `0x10000` (65536). We can combine these checks:

```powershell
Get-ADUser -Filter {PasswordNeverExpires -eq $true} -Properties UserAccountControl | Where-Object {$_.UserAccountControl -band 0x80} | Sort-Object Name | Select-Object Name
```

Alternatively, use the dedicated property:

```powershell
Get-ADUser -Filter {PasswordNeverExpires -eq $true -and AllowReversiblePasswordEncryption -eq $true} -Properties PasswordNeverExpires, AllowReversiblePasswordEncryption | Sort-Object Name | Select-Object Name
```

### Step 3: Verify with the -band 0x10000 filter

To double-check using raw `UserAccountControl` flags for both conditions:

```powershell
Get-ADUser -Filter * -Properties UserAccountControl | Where-Object {
    ($_.UserAccountControl -band 0x10000) -and   # DONT_EXPIRE_PASSWORD
    ($_.UserAccountControl -band 0x80)            # ENCRYPTED_TEXT_PWD_ALLOWED
} | Sort-Object Name | Select-Object Name
```

Output:

```
Name
----
Brandywine
Ibarra
Sanchez
```

**Answer:** `Brandywine,Ibarra,Sanchez`

---

## 20. What file on the Warrior Share contains PII?

**Answer:** `phone_matrix.xlsx`

---

## 21. What is the short domain name?

```powershell
(Get-ADDomain).NetBIOSName
```

**Answer:** `Army`

---

## 22. What is the RID of the krbtgt account?

```powershell
Get-ADUser krbtgt | Select-Object SID
```

The SID returned is something like: `S-1-5-21-1004336348-1177238915-682003330-502`

The RID is the last portion of the SID (after the final hyphen).

**Answer:** `502`

---

## 23. How many users are direct members of the Domain Admins group (not counting sub-groups)?

```powershell
(Get-ADGroupMember -Identity "Domain Admins" | Where-Object {$_.objectClass -eq "user"}).Count
```

This filters group members to only count objects of class `user`, excluding any nested groups.

**Answer:** `1`

---

## 24. What is the total number of users in Domain Admins (including nested group members)?

```powershell
(Get-ADGroupMember -Identity "Domain Admins" -Recursive).Count
```

The `-Recursive` flag expands all nested groups and counts every individual user member.

**Answer:** `14`

---

## 25. Insider Threat Trail Part 1: Who is the insider's first contact?

We already identified Karen.Nance as the insider (from Q17). Now we follow the trail starting from her AD account.

### Step 1: Pull all properties for Karen.Nance

```powershell
Get-ADUser Karen.Nance -Properties *
```

Review every field in the output. Among the many properties, one field contains an unusual string that appears to be encoded text. The string looks garbled -- it is ROT13 encoded.

### Step 2: Decode the ROT13 string

ROT13 is a simple letter substitution cipher that replaces each letter with the letter 13 positions after it in the alphabet. Decode the string found in Karen.Nance's properties:

```powershell
# ROT13 decode function
$encoded = "<ROT13_string_from_Karen's_profile>"
$decoded = -join ($encoded.ToCharArray() | ForEach-Object {
    if ($_ -match '[a-mA-M]') { [char]([int]$_ + 13) }
    elseif ($_ -match '[n-zN-Z]') { [char]([int]$_ - 13) }
    else { $_ }
})
$decoded
```

The decoded string reveals the name **Tiffany**.

### Step 3: Find the full username for Tiffany in Active Directory

```powershell
Get-ADUser -Filter {GivenName -eq "Tiffany"} | Select-Object Name, SamAccountName
```

Output:

```
Name                SamAccountName
----                --------------
Tiffany Bellacino   Tiffany.Bellacino
```

**Answer:** `Tiffany.Bellacino`

---

## 26. Insider Threat Trail Part 2: Who is the next contact?

### Step 1: Pull all properties for Tiffany.Bellacino

```powershell
Get-ADUser Tiffany.Bellacino -Properties *
```

Examine every field carefully. Several fields contain short, seemingly meaningless fragments of text scattered across different properties.

### Step 2: Identify the clue fragments

Across various AD properties (such as City, State, Department, Office, etc.), the following fragments are found:

| Field | Value |
|-------|-------|
| Field 1 | `mi` |
| Field 2 | `an` |
| Field 3 | `wis` |
| Field 4 | `da` |
| Field 5 | `le` |

### Step 3: Rearrange the fragments

At first glance the fragments seem random, but rearranging them reveals a name:

- `da` + `mi` + `an` = **Damian**
- `le` + `wis` = **Lewis**

Combined: **Damian Lewis**

### Step 4: Verify the user exists in Active Directory

```powershell
Get-ADUser -Filter {Name -like "*Damian*Lewis*"} | Select-Object Name, SamAccountName
```

Output:

```
Name          SamAccountName
----          --------------
Damian Lewis  Damian.Lewis
```

**Answer:** `Damian.Lewis`

---

## 27. Insider Threat Trail Part 3: Who is the next contact?

### Step 1: Pull all properties for Damian.Lewis

```powershell
Get-ADUser Damian.Lewis -Properties *
```

Review all fields in the output. One of the properties contains an embedded clue that references the name **Isiah**.

### Step 2: Identify the clue

Among Damian.Lewis's AD properties, a field contains text referencing or encoding the name "Isiah" -- this is the next person in the insider threat chain.

### Step 3: Search Active Directory for Isiah

```powershell
Get-ADUser -Filter {GivenName -eq "Isiah"} | Select-Object Name, SamAccountName
```

Output:

```
Name          SamAccountName
----          --------------
Isiah Jesus   Isiah.Jesus
```

**Answer:** `Isiah Jesus`

---

## 28. Insider Threat Trail Part 4: What is the final destination?

### Step 1: Pull all properties for Isiah.Jesus

```powershell
Get-ADUser Isiah.Jesus -Properties *
```

Review the output. One of the fields contains a Base64-encoded string.

### Step 2: Identify the Base64-encoded field

Among Isiah.Jesus's properties, a field contains a long string of characters that is clearly Base64 encoded (alphanumeric characters with possible `=` padding at the end).

### Step 3: Decode the Base64 string

```powershell
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("<base64_string_from_Isiah's_profile>"))
```

For example, if the Base64 string is `aHR0cHM6Ly93d3cueW91dHViZS5jb20vd2F0Y2g/dj1kUXc0dzlXZ1hjUQ==`:

```powershell
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("aHR0cHM6Ly93d3cueW91dHViZS5jb20vd2F0Y2g/dj1kUXc0dzlXZ1hjUQ=="))
```

Output:

```
https://www.youtube.com/watch?v=dQw4w9WgXcQ
```

The insider threat trail ends with a classic Rick Roll -- the final destination is a YouTube link.

**Answer:** `https://www.youtube.com/watch?v=dQw4w9WgXcQ`
