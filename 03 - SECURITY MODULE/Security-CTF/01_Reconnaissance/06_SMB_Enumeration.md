# Reconnaissance - SMB Enumeration

## Level / Points

Level 2 — 5 points

---

## Prompt

Find the SMB host and retrieve the flag.

---

## Reference Intelligence

- Target subnet: 192.168.150.224/27
- SMB host discovered at: 192.168.150.245

---

## Steps Taken

1. Set up multi-hop SSH tunnel with SOCKS proxy.
2. Conducted ping sweep of 192.168.150.224/27 to identify live hosts.
3. Ran `nmap` SMB scan against .245 to confirm SMB service.
4. Used NSE scripts for SMB enumeration to extract the flag.

---

## Verified Answer

**Answer:** `7qzvXoxE1VJXcPr84wsx`

---

## Notes

- Requires multi-hop SSH tunneling and SOCKS proxy configuration.
- NSE scripts such as `smb-enum-shares` and `smb-enum-users` are useful for SMB enumeration.
- Ping sweep the /27 subnet first to identify live hosts before targeted scanning.
