# Reconnaissance - TITLE Tags

## Challenge Info

- **Level:** 2
- **Points:** 5

## Prompt

Find the Titles of all the hosted web servers, and identify the flag.
Your flag will be a unique string of twenty random characters.

---

## Reference Intelligence

- Use `proxychains nmap -sV -Pn 192.168.28.111` to enumerate open ports and services
  (Detects live hosts and service versions through proxy)
- Ports found:
  - `80/tcp` - Apache httpd 2.4.29 (Ubuntu)
  - `2222/tcp` - OpenSSH 7.6p1 Ubuntu 4ubuntu0.3
  - `8080/tcp` - Apache httpd 2.4.29 (Ubuntu)
- Use `proxychains wget -r http://192.168.28.111` to recursively download content from port 80
  (Retrieves all accessible files from the web server on port 80 via proxy)
- Use `ls -l` to list downloaded files and inspect directory structure
- Use `proxychains wget -r http://192.168.28.111:8080` to recursively download from port 8080
  (Retrieves website content from the alternate HTTP service on port 8080)
- Use `cat index.html` to view HTML source of the main page and find TITLE tag
  (Extracts page metadata including the flag hidden in the TITLE tag)

---

## Steps Taken

1. Scanned target for open ports and service versions through proxychains and nmap

```bash
proxychains nmap -sV -Pn 192.168.28.111
```

2. Recursively downloaded website content from both HTTP ports (80 and 8080)

```bash
proxychains wget -r http://192.168.28.111
proxychains wget -r http://192.168.28.111:8080
```

3. Inspected directory contents and HTML files for clues

```bash
ls -l
cat index.html
```

4. Located the flag within the TITLE tag of the `index.html` on port 8080

---

## Verified Answer

**Flag:** `aWcn940C53OEzWwCFs3h`

---

## Notes

- Combining scanning and recursive download is effective for web reconnaissance
- Proxychains ensures traffic is routed securely or anonymously during enumeration
- Searching for flags in HTML metadata is common in CTF and penetration testing contexts
