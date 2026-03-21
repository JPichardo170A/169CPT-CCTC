# Reconnaissance - TITLE Tags

## Level / Points

Level 2 — 5 points

---

## Prompt

Find the titles of hosted web servers.

---

## Reference Intelligence

- Target: 192.168.28.111
- Ports of interest: 80, 8080

---

## Steps Taken

1. Ran `nmap 192.168.28.111` to identify open ports.
2. Used `wget -r` to recursively download content from ports 80 and 8080.
3. Inspected `index.html` for `<title>` tags containing the flag.

---

## Verified Answer

**Answer:** `aWcn940C53OEzWwCFs3h`

---

## Notes

- Check both standard HTTP (80) and alternate HTTP (8080) ports.
- Title tags are found in the `<head>` section of HTML pages.
