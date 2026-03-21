# Reconnaissance - FTP Service

## Level / Points

Level 2 — 5 points

---

## Prompt

Interrogate the FTP service and retrieve the flag.

---

## Reference Intelligence

- Target: 192.168.28.105
- Service: FTP

---

## Steps Taken

1. Used `proxychains wget -r ftp://192.168.28.105` to recursively download FTP contents.
2. Enumerated downloaded files and located the flag file among retrieved data.

---

## Verified Answer

**Answer:** `XymQAYM5n8hL1HlhsO5y`

---

## Notes

- Proxychains required for network access through tunnel.
- Recursive wget on FTP will pull all accessible directories and files.
