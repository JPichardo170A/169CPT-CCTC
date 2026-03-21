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

Filter AD accounts that are expired but still enabled, then sort by last name:

```powershell
Search-ADAccount -AccountExpired | Where-Object {$_.Enabled -eq $true} | Get-ADUser -Properties AccountExpirationDate | Sort-Object Surname | Select-Object Surname
```

**Answer:** `Krause,Page`

---

## 14. What unprofessional email domains are found in Active Directory user accounts?

Enumerate all AD users with their mail property and extract the domains:

```powershell
Get-ADUser -Filter * -Properties Mail | Where-Object {$_.Mail -ne $null} | Select-Object Mail
```

**Answer:** `ashleymadison.com`

---

## 15. What unprofessional file is found on the Warrior Share?

**Answer:** `lulz.pdf`

---

## 16. What file on the Warrior Share has modified access rights in the request?

Map the Warrior Share and search through the files:

```powershell
net use * \\<server>\Warrior Share
dir "\\<server>\Warrior Share" /s
```

**Answer:** `14287.pdf`

---

## 17. What user is requesting modified access in that file?

Extract the phone number `336-6754` from the file and search Active Directory:

```powershell
Get-ADUser -Filter * -Properties OfficePhone | Where-Object {$_.OfficePhone -like "*336-6754*"}
```

**Answer:** `Karen.Nance`

---

## 18. What accounts have unprofessional descriptions? (alphabetical order, comma-separated)

```powershell
Get-ADUser -Filter * -Properties Description | Where-Object {$_.Description -ne $null} | Sort-Object Name | Select-Object Name, Description
```

**Answer:** `Brandywine,Ibarra,Jimenez,Lee`

---

## 19. What accounts have passwords set to never expire AND store passwords using reversible encryption? (alphabetical order, comma-separated)

```powershell
Get-ADUser -Filter {PasswordNeverExpires -eq $true -and AllowReversiblePasswordEncryption -eq $true} | Sort-Object Name | Select-Object Name
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

The RID is the last portion of the SID.

**Answer:** `502`

---

## 23. How many users are direct members of the Domain Admins group (not counting sub-groups)?

```powershell
(Get-ADGroupMember -Identity "Domain Admins" | Where-Object {$_.objectClass -eq "user"}).Count
```

**Answer:** `1`

---

## 24. What is the total number of users in Domain Admins (including nested group members)?

```powershell
(Get-ADGroupMember -Identity "Domain Admins" -Recursive).Count
```

**Answer:** `14`

---

## 25. Insider Threat Trail Part 1: Who is the insider's first contact?

Inspect Karen.Nance's AD user properties, find a ROT13-encoded clue, and decode it to reveal the name Tiffany:

```powershell
Get-ADUser Karen.Nance -Properties *
```

Decode the ROT13 string to find the contact.

**Answer:** `Tiffany.Bellacino`

---

## 26. Insider Threat Trail Part 2: Who is the next contact?

Inspect Tiffany.Bellacino's AD properties and piece together the clues `mi`, `an`, `wis`, `da`, `le`:

```powershell
Get-ADUser Tiffany.Bellacino -Properties *
```

Rearrange the fragments to form the name.

**Answer:** `Damian.Lewis`

---

## 27. Insider Threat Trail Part 3: Who is the next contact?

Inspect Damian.Lewis's AD properties and find the clue referencing "Isiah":

```powershell
Get-ADUser Damian.Lewis -Properties *
```

**Answer:** `Isiah Jesus`

---

## 28. Insider Threat Trail Part 4: What is the final destination?

Inspect Isiah.Jesus's AD properties and decode the Base64-encoded string:

```powershell
Get-ADUser Isiah.Jesus -Properties *
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("<base64_string>"))
```

**Answer:** `https://www.youtube.com/watch?v=dQw4w9WgXcQ`
