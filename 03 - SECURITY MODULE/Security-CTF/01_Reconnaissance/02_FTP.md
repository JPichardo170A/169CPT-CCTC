# Reconnaissance - FTP Service Interrogation

## Challenge Info

- **Level:** 2
- **Points:** 5

## Prompt

File Transfer Protocol (FTP) appears to be available within Donovian Cyberspace. Perform further reconnaissance and interrogate this service to identify the flag. Your flag will be a unique string of twenty random characters.

---

## Reference Intelligence

- Use `proxychains wget -r ftp://192.168.28.105` to recursively download files via FTP
  (Automatically retrieves all accessible files from the FTP server through a proxy)
- Use `ls -l` to list downloaded files and verify contents
- Key files include:
  - `ServerInitialization`
  - `XymQAYM5n8hL1HlhsO5y` (flag file)
  - `welcome.msg`
- Use `cat XymQAYM5n8hL1HlhsO5y` to read the flag content

---

## Steps Taken

1. Discovered FTP service and accessed it using proxychains and wget recursive download

```bash
proxychains wget -r ftp://192.168.28.105
```

2. Listed files in the downloaded directory to identify candidate flag files

```bash
ls -l
```

3. Read the suspicious file with the random string filename to extract the flag

```bash
cat XymQAYM5n8hL1HlhsO5y
```

---

## Verified Answer

**Flag:** `XymQAYM5n8hL1HlhsO5y`

---

## Notes

- Recursive FTP download can reveal hidden or sensitive files during reconnaissance
- Random strings as filenames are often used to store flags in CTFs and pentests
- Proxychains help route FTP traffic securely or through firewalls for stealth
